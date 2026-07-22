# LetsDefend - Memory Forensics 

## Table of Contents

1. [What is Memory Forensics?](#1-what-is-memory-forensics)
2. [Memory Analysis Procedures](#2-memory-analysis-procedures)
   - Identifying malicious processes
   - Identifying malicious network connections
   - Detecting memory injections
   - Associated files (DLL, EXE)
   - Registry keys
   - Question
3. [Practical Analysis 1: Masqueraded Process](#3-practical-analysis-1-masqueraded-process)
   - Walkthrough & questions
4. [Practical Analysis 2: Process Injection](#4-practical-analysis-2-process-injection)
   - Walkthrough & questions
5. [Practical Analysis 3: DLL Injection](#5-practical-analysis-3-dll-injection)
   - Walkthrough & questions

---

## 1. What is Memory Forensics?

**Memory forensics** is the analysis of volatile data captured in a computer's memory dump. Analysts use it to identify Indicators of Compromise (IOCs) and to investigate malicious activity, behavior, and network traffic - especially on servers and critical systems that run continuously and are therefore high-value targets.

Memory is **volatile**, meaning its contents disappear the moment a computer is powered off. This has an important practical consequence: if a compromised device is simply shut down, any malware or command-and-control (C2) connections that were running in memory at that moment are lost forever, along with evidence that could have been critical to the investigation. This is exactly why **network containment** (isolating the device from the network while keeping it powered on) is generally preferred over shutting the machine down.

There's also a speed argument for memory forensics. A full incident response investigation typically requires collecting large volumes of evidence - logs, disk images, and so on - which can take hours or even days depending on their size. But in the early stages of an incident, time matters enormously for containment and response. RAM is comparatively small and quick to acquire, which makes it an excellent first artifact to pull and analyze to get a fast read on what happened on an endpoint.

Because any running program (including malware) has to reside in memory to execute, memory analysis can reveal things that may exist **only** in RAM and nowhere else: open network connections, account credentials, chat messages, encryption keys, running processes, injected code fragments, and even browsing history. This makes memory forensics one of the fastest and richest ways to detect and understand an active compromise.

**Common memory acquisition tools** (acquisition itself is covered in a separate course):
- FTK Imager
- DumpIt
- AVML
- Belkasoft RAM Capturer

The next lesson builds on this by discussing what "normal" looks like (baselines) so abnormal memory activity is easier to spot, along with the tools used to investigate it.

---

## 2. Memory Analysis Procedures

This section walks through what an analyst should specifically look for when examining a memory dump for malicious activity.

### Identifying Malicious Processes

Any malicious code has to actually run as a process to do its work, and for a process to run, its instructions and data must first be copied into RAM (e.g., opening `chrome.exe` causes the loader to copy its instructions into memory, which the CPU then executes from there). So whatever malware is doing at the time of acquisition, it will leave a footprint as a process in memory.

Every process has a unique **Process ID (PID)**, and every process (except one) has a **parent process** - the process that spawned it. For example, double-clicking a `.txt` file launches `notepad.exe` as a child of `explorer.exe`. At the very top of this hierarchy sits the `System` process, which always has PID 4 and no parent. Understanding this normal Windows process "genealogy" is essential, because attackers deliberately exploit it to hide.

To spot malicious processes, look for:

- **Suspicious or randomized process names.** Tools like Metasploit and Empire, as well as malware performing post-exploitation tasks (installing services, creating backdoors), often generate randomly-named executables. A process with an odd or generated-looking name deserves closer inspection.

- **Broken process relationships.** Threat actors frequently name their malware after legitimate Windows processes to blend in - a classic example is `lsass.exe`, the process responsible for credential management and security policy enforcement (Windows can't function without it). If an analyst doesn't know Windows's normal process genealogy, a malicious second `lsass.exe` might not raise any flags. But knowing that there should only ever be **one** `lsass.exe`, and that its parent should always be `wininit.exe`, makes a rogue one with the wrong parent immediately identifiable. (The SANS DFIR "Hunt Evil" poster is a useful reference here: https://www.sans.org/posters/hunt-evil/.) The core lesson: you have to know what's normal before you can spot what's abnormal.

- **Suspicious parent-child pairings.** For instance, seeing `winword.exe` (Microsoft Word) spawn `powershell.exe` as a child is a strong red flag - legitimate Word usage has no reason to invoke PowerShell. This pattern is extremely common in phishing attacks, where a malicious Word or Excel document triggers a payload that opens a reverse shell via PowerShell or `cmd.exe`.

- **Advanced defense evasion techniques.** When the checks above turn up nothing, more sophisticated attackers may be using techniques like **process injection**, **DLL injection**, or **process hollowing** - all of which run malicious code inside an otherwise completely legitimate process. In these cases, nothing looks wrong on the surface (correct process name, correct parent), so the analysis has to go deeper - covered in the practical cases below.

### Identifying Malicious Network Connections

Both active and recently-closed network connections leave traces in memory, which can reveal C2 IPs/domains the host has contacted - sometimes even tied back to a specific process ID, which helps confirm a suspect process.

That said, network data alone isn't a reliable primary signal: some malware may never contact a C2 server, or may not have done so yet by the time memory was acquired (some malware deliberately delays C2 contact by hours or days specifically to evade this kind of detection).

**What to look for:**
- Unusual or rare ports (deprioritize common ones like 80/443, which are expected)
- Known malicious defaults - e.g., port **4444** is Metasploit's default listener port
- Suspicious IPs or domains should be checked against threat intelligence platforms (VirusTotal, Cisco Talos)
- Beyond IP reputation, check the associated domain's registration date, nameservers, and WHOIS info - a domain registered very recently, and one an employee would have no legitimate reason to visit, is a strong indicator of malicious activity even if the IP itself isn't flagged anywhere yet.

### Detecting Memory Injections

Even when a system looks fine on the surface, injected code leaves detectable traces at the memory structure level.

The kernel-level **KDBG** structure contains a pointer called `PsActiveProcessHead`, which leads to a linked list of **EPROCESS** structures - one per active process. Each EPROCESS structure has a **VAD-Root** component, which points to **Virtual Address Descriptors (VADs)** describing regions of that process's virtual memory (memory addresses, ranges, allocated protections, and OS flags).

When code is injected into a running process, it requires additional memory, and that new memory region typically needs **read, write, and execute** permissions all at once (`EXECUTE_READWRITE`) to store and run the injected shellcode. Most legitimate programs don't need all three permissions simultaneously, which makes this combination a strong (though not perfect) red flag.

**Volatility3's `malfind` plugin** is built specifically to surface processes showing this suspicious permission pattern, along with relevant header/hex data explaining why the extra memory was needed. One important caveat: some legitimate, dynamic programs - most notably `MsMpEng.exe` (Microsoft's antimalware engine) - also need these permissions to actively scan other processes' memory, and will regularly show up in `malfind` output as a **false positive**. Because attackers can also disguise malware using that same filename, results still need to be validated (e.g., by examining code headers or tracing the process's legitimacy) rather than dismissed outright.

### Associated Files (DLL, EXE)

Most programs depend on supporting files - DLLs and others - to function and to interact with the operating system. Once a malicious process is identified, these associated files also need to be retrieved and analyzed to fully understand what the malware actually did on the infected system. (DLL injection specifically - where an attacker forces a legitimate process to load a malicious DLL - is covered in depth in Practical Analysis 3 below.)

### Registry Keys

The Windows registry stores information ("keys") about nearly everything that happens on the OS, making it one of the most valuable artifacts in host forensics - often one of the very first things examined. While most registry keys can technically be pulled from a memory dump, it's generally preferable to acquire them directly from disk instead (a topic for a future Windows forensics course).

### Question

**1. Which plugin in volatility3 aids us in finding memory injections?**
Answer: `malfind`

---

## 3. Practical Analysis 1: Masqueraded Process

**Scenario:** In a normal Windows session it's common to see 8-9+ instances of `svchost.exe`, all of which should share `services.exe` as their parent process. This case walks through spotting one that doesn't.

**Step 1 - List running processes:**
```bash
python3 /home/analyst/volatility3/vol.py -f /home/analyst/memorydumps/RogueProcessCase1.mem windows.pslist
```
This shows `services.exe` (PID 604) correctly parented by `wininit.exe`, and more than ten `svchost.exe` processes correctly parented by PID 604 - except one, which has **PID 5688** and a parent PPID of **3664**, breaking the expected pattern.

**Step 2 - Trace the rogue parent (PPID 3664):**
Grepping for that PPID reveals it belongs to `explorer.exe` - meaning someone physically double-clicked the malicious file from File Explorer, which is itself a bad sign.

**Step 3 - Inspect its command-line arguments:**
```bash
python3 /home/analyst/volatility3/vol.py -f /home/analyst/memorydumps/RogueProcessCase1.mem windows.cmdline | grep 5688
```
This reveals the file was launched from the Downloads folder of user "CyberJunkie."

**Step 4 - Check for network activity tied to this PID:**
```bash
python3 /home/analyst/volatility3/vol.py -f /home/analyst/memorydumps/RogueProcessCase1.mem windows.netscan | grep 5688
```
Shows a connection to `192.168.230.128:53` - an internal address in this lab exercise, but in a real investigation this IP (and any unusually high or well-known malicious ports, like Metasploit's 4444) would be checked against threat intelligence sources.

**Step 5 - Dump the binary for further analysis:**
```bash
python3 /home/analyst/volatility3/vol.py -f /home/analyst/memorydumps/RogueProcessCase1.mem windows.pslist --dump --pid 5688
```
Submitting the dumped binary to VirusTotal confirms it's a **Metasploit payload executable**.

**Takeaway:** once a malicious file like this is found, the IOCs (file hash, IP address) should be used to hunt for other infected machines across the network. This particular masquerading technique is older and less common today thanks to improved EDR and defensive tooling - but it's still a good foundational example of process-relationship analysis.

### Questions

**1. Connect to the machine and examine the memory dump (/root/Desktop/QuestionFiles/RogueProcessCase1.mem). What's the Process ID of the process "dllhost.exe"?**
Filtered the process list for that process name:
```bash
python3 Tools/volatility3/vol.py -f ~/Desktop/QuestionFiles/RogueProcessCase1.mem windows.pslist | grep dllhost.exe
```

**2. What are the command line arguments of Process ID "4204"?** *(Answer format: `"C:\Users\CyberJunkie\Desktop\cmd.exe" /test`)*
Filtered the cmdline plugin output by PID:
```bash
python3 Tools/volatility3/vol.py -f ~/Desktop/QuestionFiles/RogueProcessCase1.mem windows.cmdline | grep 4204
```

**3. To which IP address and port is Process ID "2696" connected?** *(Answer starts with 23; format `IP:Port`)*
Filtered the netscan plugin output by PID:
```bash
python3 Tools/volatility3/vol.py -f ~/Desktop/QuestionFiles/RogueProcessCase1.mem windows.netscan | grep 2696
```

![mem1](images/mem1.png)

---

## 4. Practical Analysis 2: Process Injection

**Process injection** is a technique where an attacker runs code inside the address space of a different, legitimate process. This lets malicious code execute "under the umbrella" of a trusted process name, helping it evade simple detection methods and hide its tracks.

**Step 1 - List processes and command lines:**
```bash
python3 /home/analyst/volatility3/vol.py -f /home/analyst/memorydumps/ProcessInjectionCase2.mem windows.pslist
python3 /home/analyst/volatility3/vol.py -f /home/analyst/memorydumps/ProcessInjectionCase2.mem windows.cmdline
```
Nothing suspicious jumps out here - no odd names, no broken parent/child relationships. This is expected with injection-based evasion, since the process itself is entirely legitimate; only its memory contents are compromised.

**Step 2 - Run `malfind` to look for injected code based on memory permissions:**
```bash
python3 /home/analyst/volatility3/vol.py -f /home/analyst/memorydumps/ProcessInjectionCase2.mem windows.malfind
```
Two processes are flagged:
- `svchost.exe` (PID **1776**) - showing the suspicious `EXECUTE_READWRITE` permission pattern discussed earlier. This is not normal behavior for `svchost.exe`.
- `MsMpEng.exe` - a known **false positive**, since Microsoft Defender's engine legitimately needs these permissions to scan other processes' memory. This is a good real-world reminder to validate rather than assume, since attackers can also disguise malware under this same name.

**Step 3 - Interpret the flagged data:**
The hex, ASCII, and assembly output associated with the `svchost.exe` finding matches known patterns seen in **C2 beacons / Meterpreter shellcode** - confirming that Meterpreter shellcode was injected into this process (chosen deliberately here since `svchost.exe` was also the vehicle in Practical Case 1).

**Step 4 - Check for related network activity:**
```bash
python3 /home/analyst/volatility3/vol.py -f /home/analyst/memorydumps/ProcessInjectionCase2.mem windows.netscan | grep 1776
```
Reveals a connection to `192.168.230.128` on port **4444** - Metasploit's default port - confirming active C2 communication.

**IOCs identified:**
- `192.168.230.128`
- `svchost.exe` (PID 1776) and its hash

In a real incident, the next steps would be blocking the IP and hunting the network for the same IOCs before deeper forensic work continues.

### Questions

**Connect to the machine and examine the memory dump (/root/Desktop/QuestionFiles/ProcessInjectionCase2.mem).**

**What's the process ID of dllhost.exe?**
Answer: `3912`

**There can be false positives when looking for process injections. (True/False)**
Answer: `True` - as demonstrated by the `MsMpEng.exe` example above.

**What is the command line argument of the process with PID 1236?**
Answer: `C:\Windows\System32\svchost.exe -k LocalServiceNetworkRestricted -p`

**What is the end of the memory address of the injected process?**
Answer: `0x1e45a3d0fff`

---

## 5. Practical Analysis 3: DLL Injection

**DLL injection** is closely related to process injection, but instead of injecting raw shellcode directly, the attacker forces a legitimate process to load a **malicious DLL**, which then executes the attacker's code under that process's context.

**Step 1 - List processes:**
```bash
python3 /home/analyst/volatility3/vol.py -f /home/analyst/memorydumps/DllInjectionCase3.mem windows.pslist
```
Again, nothing looks obviously wrong at a glance, so the investigation moves straight to hunting for the injected component.

**Step 2 - Run `malfind`:**
```bash
python3 /home/analyst/volatility3/vol.py -f /home/analyst/memorydumps/DllInjectionCase3.mem windows.malfind
```
`spoolsv.exe` (the Windows Print Spooler service, responsible for handling print jobs) is flagged. Notably, instead of a raw shellcode header (as seen in the process-injection case), the output shows an actual **file path** - because a DLL was injected rather than raw code. The path points to `Winsrvc.dll` sitting in a Downloads folder. This is doubly suspicious: DLLs don't normally live in a Downloads folder, and while the name sounds like a plausible Windows system DLL, no such DLL natively exists in Windows.

**Step 3 - Confirm and extract the suspicious DLL:**
```bash
python3 /home/analyst/volatility3/vol.py -f /home/analyst/memorydumps/DllInjectionCase3.mem windows.dlllist | grep Winsrvc
python3 /home/analyst/volatility3/vol.py -f /home/analyst/memorydumps/DllInjectionCase3.mem windows.dlllist --pid 1656 --dump
```
Submitting the extracted DLL to VirusTotal confirms it's malicious - built using **MSFvenom** to establish a reverse connection.

**Step 4 - Check network activity:**
```bash
python3 /home/analyst/volatility3/vol.py -f /home/analyst/memorydumps/DllInjectionCase3.mem windows.netscan | grep rundll32
```
Interesting detail: the actual C2 connection shows up under `rundll32.exe`, not `spoolsv.exe` - because in Windows, DLL execution is generally handled by `rundll32.exe`, even though the code runs in the context of the process it was injected into (`spoolsv.exe`). Same attacker IP as the previous cases turns up again.

**IOCs identified:**
- `192.168.230.128`
- `Winsrvc.dll` and its hash

### Questions

**Connect to the machine and examine the memory dump (/root/Desktop/QuestionFiles/DLLInjection-Case3.mem).**

**1. What's the name of the last DLL of the process with the PID "996"?**
Listed all DLLs for that PID and checked the last entry in the output:
```bash
python3 ~/Desktop/Tools/volatility3/vol.py -f ~/Desktop/QuestionFiles/DLLInjection-Case3.mem windows.dlllist --pid 996
```
Result: `DAVHLPR.dll`

**2. What's the malicious DLL full path?**
Started with `malfind` to spot a process whose flagged memory region contained a Windows file path in its hex output:
```bash
python3 ~/Desktop/Tools/volatility3/vol.py -f ~/Desktop/QuestionFiles/DLLInjection-Case3.mem windows.malfind
```
Then filtered the DLL list by that process's name to confirm the same path appeared as its most recently loaded DLL:
```bash
python3 ~/Desktop/Tools/volatility3/vol.py -f ~/Desktop/QuestionFiles/DLLInjection-Case3.mem windows.dlllist | grep spoolsv
```
Result: `c:\users\CyberJunkie\Downloads\Winsrvc.dll`

**3. What's the port number on which C2 connection is made?**
Used the netscan plugin, filtering by the process actually handling the DLL execution:
```bash
python3 ~/Desktop/Tools/volatility3/vol.py -f ~/Desktop/QuestionFiles/DLLInjection-Case3.mem windows.netscan | grep rundll32
```

**4. What would be the Volatility 3 command to dump hashes from LSASS?** *(Answer format: `python3 vol.py -f memory.dump XXX.XXX`)*
```bash
python3 vol.py -f memory.dump windows.hashdump
```