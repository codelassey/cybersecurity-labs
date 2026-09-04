# Windows Forensic Analysis Playbook
### A Practical Reference for Investigators and Learners

>*This playbook is intended as a practical reference guide to support DFIR practitioners understand critical Windows artifacts, where they are stored, and their role in forensic investigations, rapid response scenarios, and ongoing learning. I believe it is not exhaustive.. Windows forensic analysis is a deep and evolving field. Anyone reading my compilation should treat this as a good starting point*
---

## Table of Contents

1. [Introduction](#1-introduction)
2. [Core Concepts: What Are Windows Artifacts?](#2-core-concepts-what-are-windows-artifacts)
3. [Quick Reference: Artifact Matrix](#3-quick-reference-artifact-matrix)
4. [Jump Lists](#4-jump-lists)
5. [LNK (Shortcut) Files](#5-lnk-shortcut-files)
6. [RecentDocs / OpenSave MRU](#6-recentdocs--opensave-mru)
7. [Prefetch Files](#7-prefetch-files)
8. [SRUM (System Resource Usage Monitor)](#8-srum-system-resource-usage-monitor)
9. [Windows Event Logs](#9-windows-event-logs)
10. [Recycle Bin ($I and $R Files)](#10-recycle-bin-i-and-r-files)
11. [Windows Search Index](#11-windows-search-index)
12. [RDP Bitmap Cache](#12-rdp-bitmap-cache)
13. [ThumbCache](#13-thumbcache)
14. [Artifact Correlation: Building a Timeline](#14-artifact-correlation-building-a-timeline)
15. [Forensic Tooling Overview](#15-forensic-tooling-overview)
16. [Investigative Scenarios & Case Examples](#16-investigative-scenarios--case-examples)
17. [References and Resources](#17-references-and-resources)

---

## 1. Introduction

This playbook is a structured, practical reference for performing Windows forensic analysis. It is designed for both cybersecurity professionals and learners who want to understand how Windows systems generate, store, and retain forensic artifacts - and how those artifacts can be collected, parsed, and interpreted during an investigation.

The playbook explains not just *what* each artifact is, but *where* to find it, *how* to extract and parse it, *what investigative questions* it answers, and *how to correlate* it with other artifacts to build defensible evidence.

### Who Is This For?

- **Incident responders** who need to rapidly triage compromised Windows hosts
- **Digital forensic investigators** building user activity timelines
- **Students and learners** preparing for certifications like GCFE, GCFA, or GCIH
- **Threat hunters** looking for persistence mechanisms and lateral movement evidence

### Guiding Principles

1. **Collect before you analyze.** Many Windows artifacts have short retention windows (e.g., SRUM: 30–60 days). Triage and image quickly.
2. **No single artifact tells the whole story.** Correlate multiple sources to build robust timelines.
3. **Context matters.** A LNK file proves presence of a file path - not that the file was read, modified, or exfiltrated. Always interpret artifacts within the broader investigation.
4. **Document your methodology.** Forensic findings must be reproducible and defensible.

---

## 2. Core Concepts: What Are Windows Artifacts?

Windows artifacts are pieces of data created, modified, or stored by the Windows operating system as a by-product of normal activity. They are not designed as forensic evidence - they are created for performance, user experience, or system operation. This makes them powerful: a suspect cannot easily suppress them because they may not even know they exist.

Artifacts broadly fall into these categories:

| Category | Description | Examples |
|---|---|---|
| **User Activity** | Records what files, applications, and folders a user accessed | Jump Lists, LNK Files, MRU Keys |
| **Execution Evidence** | Proves that a program ran on the system | Prefetch, SRUM, BAM/DAM |
| **Network Activity** | Tracks network connections and data volumes | SRUM, Event Logs (4624/4625) |
| **File System Metadata** | Records file presence, deletion, and movement | Recycle Bin, ThumbCache, Search Index |
| **System Events** | Logs authentication, service creation, and policy changes | Windows Event Logs |
| **Visual Artifacts** | Captures graphical residue of user sessions | ThumbCache, RDP Bitmap Cache |

---

## 3. Quick Reference: Artifact Matrix

The table below provides an at-a-glance view of all artifacts covered in this playbook - including storage paths, retention behavior, and investigative value.

| Artifact | Path | Retention / Persistence | Investigative Value |
|---|---|---|---|
| **Jump Lists (Automatic)** | `%USERPROFILE%\AppData\Roaming\Microsoft\Windows\Recent\AutomaticDestinations\` | Persists after source file/app deleted | Auto-created file/app access history - file path, access count, access and creation timestamps |
| **Jump Lists (Custom)** | `%USERPROFILE%\AppData\Roaming\Microsoft\Windows\Recent\CustomDestinations\` | Persists after source file/app deleted | User-pinned file/app access history - same data as above, different trigger (pinning vs. opening) |
| **LNK Files** | `C:\Users\<user>\AppData\Roaming\Microsoft\Windows\Recent\` | Persists after source file deleted | File access proof; target path, timestamps, volume serial number - survives source deletion |
| **RecentDocs MRU** | `NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\RecentDocs` | Until registry key is cleared | Most recently accessed files per extension; ordered access list |
| **OpenSave MRU** | `NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\ComDlg32\OpenSavePidlMRU` | Until registry key is cleared | Files opened/saved via Windows common dialogs; extension-specific tracking |
| **Prefetch Files** | `C:\Windows\Prefetch\*.pf` | Up to 1,024 files; oldest may be overwritten | Execution evidence - binary name, run count, last 8 run times, DLLs loaded |
| **SRUM Database** | `C:\Windows\System32\SRU\SRUDB.dat` | ~30–60 days rolling window | Execution evidence + resource/network usage stats per app per user account. Requires SOFTWARE hive for recent entries |
| **Windows Event Logs** | `C:\Windows\System32\winevt\Logs\*.evtx` | Cyclical - overwritten when full | Authentication, persistence, lateral movement, service creation |
| **Recycle Bin ($I files)** | `C:\$Recycle.Bin\{SID}\$I######` | Until bin is emptied | Metadata only - original path, size, deletion timestamp; survives even if $R content is gone |
| **Recycle Bin ($R files)** | `C:\$Recycle.Bin\{SID}\$R######` | Until permanently deleted | Actual recoverable file content |
| **Windows Search Index** | `C:\ProgramData\Microsoft\Search\Data\Applications\Windows\Windows.edb` | A few days after deletion, then purged | Partial file content recovery (docx, pdf, txt, etc.) + browser history; runs silently |
| **RDP Bitmap Cache** | `C:\Users\<username>\AppData\Local\Microsoft\Terminal Server Client\Cache\` | Persists per user profile | BMP tile fragments of what was displayed during an RDP session - key for lateral movement evidence |
| **ThumbCache** | `C:\Users\<username>\AppData\Local\Microsoft\Windows\Explorer\` (Thumbcache_xxx.db, iconcache_xxx.db) | Persists after source file deleted | Thumbnail previews of images, documents, and videos; proves file existed and was viewed |

---

## 4. Jump Lists

### What Are Jump Lists?

Jump Lists were introduced in Windows 7. They are accessed through the taskbar by right-clicking an application icon, and they show the user recently or frequently accessed files for that application. Windows creates these automatically (Automatic Destinations) and applications can also add custom entries (Custom Destinations).

Each Jump List entry is a shell item containing metadata about the file that was accessed, including the file path, timestamps, and the volume it resided on.

### Why They Matter

Jump Lists track up to approximately 2,000 recently or frequently accessed files per application, per user account. Because they are named using the globally unique Application ID (AppID) of the corresponding application, they can be matched to specific programs.

**Key investigative values:**
- Connect a user account to specific files they opened via specific applications
- Persist even after the source file has been deleted
- Record both access timestamps and target file timestamps
- Created the first time an application executes - so they also serve as execution evidence

**Caveat:** Jump Lists are created on first application use by the user account. They cannot be used to determine which applications are merely pinned to the taskbar but never run.

### Storage Location

```
C:\Users\<username>\AppData\Roaming\Microsoft\Windows\Recent\AutomaticDestinations\
C:\Users\<username>\AppData\Roaming\Microsoft\Windows\Recent\CustomDestinations\
```

- **Automatic Destinations:** Created automatically by Windows for each application
- **Custom Destinations:** Created by the application developer to highlight tasks/actions

### Step-by-Step: Collecting Jump Lists

1. Navigate to or image the user profile's `AppData\Roaming\Microsoft\Windows\Recent\` directory.
2. Copy both `AutomaticDestinations` and `CustomDestinations` folders.
3. Files are named by AppID (e.g., `9b9cdc69c1c24e2b.automaticDestinations-ms`).
4. Use a forensic tool to parse the files (see tools below).

### Step-by-Step: Parsing with JLECmd

```bash
JLECmd.exe -d AutomaticDestinations --csv output-folder --csvf JumpLists.csv
```

**What each flag does:**
- `-d AutomaticDestinations` - processes all Jump List files in that directory
- `--csv output-folder` - exports results to the specified folder
- `--csvf JumpLists.csv` - names the CSV output file

**Output fields to examine:**
- `SourceFile` - the AppID-named Jump List file
- `TargetIDAbsolutePath` - full path to the accessed file
- `TargetCreated`, `TargetModified`, `TargetAccessed` - timestamps of the target file
- `LastAccessDate` - when the Jump List entry was last updated
- `VolumeLabel`, `VolumeSerialNumber` - volume information (can identify external drives)

### Tools

| Tool | Type | Link |
|---|---|---|
| JumpList Explorer | GUI | https://ericzimmerman.github.io/#!index.md |
| JLECmd | CLI | https://github.com/EricZimmerman/JLECmd |
| TZWorks jmp | CLI | https://tzworks.com/prototype_page.php?proto_id=20 |
| Velociraptor | Endpoint | https://docs.velociraptor.app/artifact_references/pages/windows.forensics.jumplists |

### Investigative Correlation

Pair Jump List findings with:
- **Prefetch** - to confirm the application actually executed
- **LNK Files** - to corroborate file access timestamps
- **RecentDocs / MRU** - to cross-validate the order of file access
- **SRUM** - to confirm the application was running and consuming resources at the relevant time

---

## 5. LNK (Shortcut) Files

### What Are LNK Files?

LNK (shortcut) files are automatically generated by Windows whenever a user creates or opens a file or folder. They contain metadata about the accessed item - including the full file path, timestamps (creation, access, modification), file size, and volume or network share information such as volume serial numbers.

LNK files have their own filesystem metadata (creation and modification times), which indicates when the file or folder at the target path was first and last accessed.

### Why They Matter

LNK files are valuable because they **survive the deletion of the original file**. Even if a suspect deletes a sensitive document, the LNK file may remain and prove the file existed and was accessed.

**Key investigative values:**
- Prove a specific file existed on the system and was accessed
- Identify files accessed from removable media or network shares via volume serial number
- Residual evidence even after the original file is gone
- Useful for exfiltration staging investigations

**Caveat:** LNK files are created not only when a file is opened, but also for parent and grandparent folders. The existence of an LNK file proves the file path was accessed - not necessarily that the file contents were read or modified. Interpret alongside other artifacts.

### Storage Location

```
C:\Users\<user>\AppData\Roaming\Microsoft\Windows\Recent\
```

The Recent folder shows approximately 149 LNK files via Explorer, but many more may be accessible via the command line or forensic tools.

### Step-by-Step: Collecting LNK Files

1. Image or copy the `C:\Users\<user>\AppData\Roaming\Microsoft\Windows\Recent\` directory.
2. Note that LNK files may also be created on the Desktop, in Send To folders, and in other locations.
3. Preserve the files with their original filesystem timestamps - do not simply copy them with Windows Explorer if avoidable.

### Step-by-Step: Parsing with LECmd

```bash
LECmd.exe -d "%AppData%\Microsoft\Windows\Recent" --csv output-folder --csvf LNK.csv
```

**What each flag does:**
- `-d` - specifies the directory to process
- `--csv output-folder` - exports results to the specified folder
- `--csvf LNK.csv` - names the CSV output file

**Key output fields to examine:**
- `TargetCreated`, `TargetModified`, `TargetAccessed` - timestamps of the file that was accessed
- `LocalPath` / `NetworkSharePath` - where the file was located
- `VolumeSerialNumber` - identifies the volume (can match a seized USB drive)
- `FileSize` - size of the target file at time of access
- `HeaderCreated` / `HeaderModified` - LNK file's own timestamps

### Tools

| Tool | Type | Link |
|---|---|---|
| LECmd | CLI | https://github.com/EricZimmerman/LECmd |
| TZWorks lp | CLI | https://tzworks.com/prototype_page.php?proto_id=11 |
| Velociraptor | Endpoint | https://docs.velociraptor.app/artifact_references/pages/windows.forensics.lnk |

### Investigative Correlation

- **Jump Lists** - corroborate which application opened the file
- **RecentDocs MRU** - confirm file access order
- **SRUM** - if the file was transferred, check network data volumes at that time
- **USB artifacts / Registry** - match volume serial numbers to identify specific removable media

---

## 6. RecentDocs / OpenSave MRU

### What Are RecentDocs and OpenSave MRU?

These are Windows Registry keys that track the most recently accessed files and folders by a user, and the **order** in which they were accessed. They are stored in the user's `NTUSER.DAT` registry hive.

- **RecentDocs** populates "Recent" menus in Start menus and File Explorer.
- **OpenSave MRU (OpenSavePidlMRU)** tracks files that have been opened or saved via Windows common dialog boxes (the standard Open/Save file picker).

Both keys store file names, extensions, and sometimes directory paths - allowing investigators to reconstruct recent user activity even without other corroborating artifacts.

### Why They Matter

**Key investigative values:**
- Show which files were accessed and in what order - critical for insider threat timelines
- Reveal hidden, deleted, or unusual directories a user accessed
- Track access across file extensions separately (e.g., all recently accessed `.xlsx` files)
- The most recently accessed item correlates with a timestamp

**Caveat:** Both keys only show that files were opened - they do not indicate how the files were used or whether they were modified.

### Storage Location

```
NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\RecentDocs
NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\ComDlg32\OpenSavePidlMRU
```

**How much data:**
- RecentDocs: stores 150 of the most recent items in the parent key, with up to 20 of each extension (30 for folders or files without an extension) in subkeys
- OpenSavePidlMRU: stores the 20 most recently accessed files of any extension in the `*` key and 20 of each extension in subkeys

### Step-by-Step: Collecting MRU Keys

1. Acquire the `NTUSER.DAT` from each user's profile folder (`C:\Users\<username>\`).
2. Note: `NTUSER.DAT` is locked while the user is logged in. Use a forensic imaging tool or Volume Shadow Copy to access it live.
3. Parse with Registry Explorer or KAPE.

### Step-by-Step: Parsing with KAPE

```bash
.\kape.exe --msource source-volume --mdest output-folder --module RECmd_UserActivity --mef csv
```

**What each flag does:**
- `--msource source-volume` - processes evidence in the specified volume
- `--mdest output-folder` - exports parsed results to the output folder
- `--module RECmd_UserActivity` - runs the built-in RECmd_UserActivity module (parses RecentDocs, OpenSavePidlMRU, and other user activity artifacts)
- `--mef csv` - exports results to CSV format

### Tools

| Tool | Type | Link |
|---|---|---|
| Registry Explorer | GUI | https://ericzimmerman.github.io/#!index.md |
| RECmd | CLI | https://ericzimmerman.github.io/#!index.md |
| KAPE | CLI/GUI | https://www.kroll.com/en/services/cyber/incident-response-recovery/kroll-artifact-parser-and-extractor-kape |
| Velociraptor | Endpoint | https://docs.velociraptor.app/artifact_references/pages/windows.registry.recentdocs |

### Investigative Correlation

- **LNK Files** - corroborate specific file access events
- **Jump Lists** - confirm which application was used to open the files
- **SRUM** - correlate with application network usage if exfiltration is suspected

---

## 7. Prefetch Files

### What Are Prefetch Files?

Prefetch files (`.pf`) are created by Windows to speed up application launching. Each time an executable runs, Windows records information about it in a Prefetch file. These files serve as some of the **strongest evidence of program execution** available in Windows forensics.

Each `.pf` file records:
- The name and path of the executable
- The total run count
- The last **eight** timestamps when the executable ran
- DLLs and files the executable loaded during its last execution

### Why They Matter

**Key investigative values:**
- Prove a program ran on the system - even if it has since been deleted
- Record how many times it ran and when
- The first execution time can be estimated as the PF file creation time minus up to 10 seconds
- Can reveal what files and DLLs malware loaded, aiding in understanding its behavior

**Caveat:** Prefetch is often **disabled by default on Windows Servers**. Always verify whether it is enabled on the target system before concluding a binary did not run.

### Storage Location

```
C:\Windows\Prefetch\*.pf
```

There can be up to 1,024 `.pf` files. When the limit is reached, the oldest files are overwritten.

### Step-by-Step: Collecting Prefetch Files

1. Acquire the entire `C:\Windows\Prefetch\` directory.
2. Prefetch files are readable without the system being live - copy them directly from an image.
3. Note the filesystem creation timestamp on each `.pf` file - this approximates the first execution time.

### Step-by-Step: Parsing with PECmd

```bash
PECmd.exe -d "C:\Windows\Prefetch" --csv output-folder --csvf prefetch.csv
```

**What each flag does:**
- `-d "C:\Windows\Prefetch"` - processes all Prefetch files in the directory
- `--csv output-folder` - exports results to the specified output folder
- `--csvf prefetch.csv` - names the CSV output file

**Key output fields to examine:**
- `ExecutableName` - the name of the binary that ran
- `RunCount` - total number of times the executable was launched
- `LastRun` - most recent execution timestamp
- `PreviousRunTimes` - up to seven previous execution timestamps
- `FilesLoaded` - list of files and DLLs loaded by the executable

### Tools

| Tool | Type | Link |
|---|---|---|
| PECmd | CLI | https://github.com/EricZimmerman/PECmd |
| WinPrefetchView | GUI | https://www.nirsoft.net/utils/win_prefetch_view.html |
| TZWorks pf | CLI | https://tzworks.com/prototype_page.php?proto_id=1 |
| Velociraptor | Endpoint | https://docs.velociraptor.app/artifact_references/pages/windows.forensics.prefetch/ |

### Investigative Correlation

- **Jump Lists** - confirm the application was used to access specific files
- **SRUM** - correlate execution with network activity
- **BAM/DAM (Background Activity Monitor)** - additional execution evidence for modern Windows 10/11 systems
- **Event Logs (ID 4688)** - if process creation auditing is enabled, confirms execution with additional context

---

## 8. SRUM (System Resource Usage Monitor)

### What Is SRUM?

The System Resource Usage Monitor (SRUM) is a Windows feature introduced in **Windows 8**, enabled by default. It tracks system and application activity in approximately **hourly increments**. The data powers Task Manager's performance views, but forensically it is a goldmine - providing a detailed log of applications launched, user accounts that ran them, and per-application network usage.

SRUM data is stored in an ESE (Extensible Storage Engine) database: `SRUDB.dat`.

### Why It Matters

**Key investigative values:**
- Identify applications running on the system and which user accounts ran them
- Quantify how much data each application sent and received over the network
- Identify which network interface was used
- Execution evidence that can survive binary deletion
- Critical for detecting exfiltration - shows WinSCP, rclone, or similar tools moving large data volumes

**Caveat:**
- Data is only retained for **30–60 days** - collect SRUM quickly after an incident.
- Entries are written in periodic blocks, so exact activity times cannot always be pinpointed.
- The `SRUDB.dat` is not always flushed to disk on shutdown - some recent entries may be missed.
- The SOFTWARE registry hive is required alongside `SRUDB.dat` to translate identifiers (app names, user SIDs) into readable values.

### Storage Location

```
C:\Windows\System32\SRU\SRUDB.dat
SOFTWARE\Microsoft\Windows NT\CurrentVersion\SRUM\Extensions  (Registry key for table names)
```

### Step-by-Step: Collecting SRUM Data

1. Copy `C:\Windows\System32\SRU\SRUDB.dat` from the system image.
2. Also export the SOFTWARE registry hive (`C:\Windows\System32\config\SOFTWARE`) - required to resolve application and user identifiers.
3. If collecting live, use a tool that can access locked files (e.g., VSS or raw copy utilities).

### Step-by-Step: Parsing with srum-dump

```bash
srum_dump.exe -i SRUDB.dat -o output-folder -r SOFTWARE -f xls
```

**What each flag does:**
- `-i SRUDB.dat` - specifies the SRUM database file
- `-o output-folder` - outputs parsed data into the specified folder
- `-r SOFTWARE` - parses the SOFTWARE registry hive to resolve identifiers
- `-f xls` - exports results to XLS format

### Step-by-Step: Parsing with SRUMECmd (Alternative)

```bash
SrumECmd.exe -f SRUDB.dat -r SOFTWARE --csv output-folder
```

**Key tables to examine:**
- **Network Usage table** - bytes sent/received per application, per user account
- **Application Resource Usage table** - CPU time, memory usage per application
- **Network Connectivity table** - which network interface/profile was active

### Tools

| Tool | Type | Link |
|---|---|---|
| srum-dump | CLI | https://github.com/MarkBaggett/srum-dump |
| SRUMECmd | CLI | https://github.com/EricZimmerman/Srum |
| Velociraptor | Endpoint | https://docs.velociraptor.app/artifact_references/pages/windows.forensics.srum/ |

### Investigative Correlation

- **Prefetch** - cross-reference to confirm what applications ran
- **Event Logs** - correlate with authentication events to confirm who was logged in
- **Jump Lists / LNK** - tie specific files to the applications SRUM shows running
- **Network logs / firewall logs** - validate the data volumes SRUM reports

---

## 9. Windows Event Logs

### What Are Windows Event Logs?

Windows Event Logs (`.evtx` files) record detailed system, application, and user account activity. They include user logon attempts, service creation, network connections, process execution, and many other events.

There are three primary event logs:
- **Security** - most valuable for investigations; authentication, privilege use, object access
- **System** - hardware events, service start/stop, driver errors
- **Application** - application-level events

Additionally, hundreds of custom/operational logs exist in `Applications and Services Logs`.

### Why They Matter

**Key investigative values:**
- Attribute actions to specific user accounts, applications, or system processes
- Build timelines for authentication, persistence, and lateral movement
- Document attacker TTPs directly from system evidence
- Well-documented event IDs - rare for Windows artifacts

**Caveat:** Logs are cyclical - when the log file reaches its maximum size, older events are overwritten. By default, many high-value events (failed logons, process creation, RDP connections) are **not enabled**. Always check audit policy on the investigated system.

### Storage Location

```
C:\Windows\System32\winevt\Logs\*.evtx
```

### High-Value Event IDs

| Event ID | Log | Description |
|---|---|---|
| **4624** | Security | Successful logon - includes logon type and source |
| **4625** | Security | Failed logon attempt |
| **4648** | Security | Logon with explicit credentials (e.g., runas) |
| **4688** | Security | Process creation (requires audit policy) |
| **4698** | Security | Scheduled task created |
| **4720** | Security | User account created |
| **4728 / 4732** | Security | User added to privileged group |
| **7034** | System | Service crashed unexpectedly |
| **7035** | System | Service control manager sent start/stop |
| **7036** | System | Service entered running/stopped state |
| **7045** | System | New service installed - high value for persistence detection |
| **1102** | Security | Audit log cleared - indicates anti-forensics |
| **4776** | Security | NTLM authentication attempt |

### Logon Types Reference

| Logon Type | Description |
|---|---|
| 2 | Interactive (console login) |
| 3 | Network (share access, SMB) |
| 4 | Batch (scheduled tasks) |
| 5 | Service |
| 7 | Unlock |
| 10 | RemoteInteractive (RDP) |
| 11 | CachedInteractive |

### Step-by-Step: Collecting Event Logs

1. Acquire all `.evtx` files from `C:\Windows\System32\winevt\Logs\`.
2. Also consider custom operational logs in `Applications and Services Logs` (e.g., PowerShell, Task Scheduler, WMI).
3. If investigating remotely, use `wevtutil` or KAPE to collect.

### Step-by-Step: Parsing with EvtxECmd

```bash
EvtxECmd.exe -d Logs --csv output-folder --csvf EventLogs.csv
```

**What each flag does:**
- `-d Logs` - processes all EVTX files in the Logs directory
- `--csv output-folder` - exports parsed results
- `--csvf EventLogs.csv` - names the output CSV

**Filtering in Event Log Explorer (GUI):**
1. Open Event Log Explorer and load the `.evtx` file.
2. Use Filter - Event ID to narrow to specific IDs (e.g., 4624, 7045).
3. Add columns for TimeCreated, EventID, UserID, Computer, and relevant message fields.
4. Export to XLSX, PDF, or HTML for reporting.

### Tools

| Tool | Type | Link |
|---|---|---|
| Event Log Explorer | GUI | https://www.eventlogxp.com |
| EvtxECmd | CLI | https://github.com/EricZimmerman/evtx |
| Chainsaw | CLI | https://github.com/WithSecureLabs/chainsaw |
| Velociraptor | Endpoint | https://docs.velociraptor.app/docs/forensic/event_logs |

### Investigative Correlation

- **Prefetch** - if Event ID 4688 is present, cross-reference with Prefetch for execution context
- **SRUM** - correlate logon events (4624) with application activity
- **Task Scheduler logs** - pair with Event ID 4698 to identify malicious scheduled tasks
- **RDP Bitmap Cache** - if lateral movement via RDP is suspected, correlate with Event IDs 4624 (Type 10) and RDP cache artifacts

---

## 10. Recycle Bin ($I and $R Files)

### What Are Recycle Bin Artifacts?

When a user deletes a file in Windows (sending it to the Recycle Bin rather than permanently deleting it), the system creates two files in the Recycle Bin folder:

- **`$I######`** - a metadata file containing the original file path, original file size, and deletion timestamp
- **`$R######`** - the actual file content (recoverable if not yet emptied)

These files are stored per-user in a folder named after the user's Security Identifier (SID).

### Why They Matter

**Key investigative values:**
- `$I` files persist as metadata **even after the $R file (content) is gone** - proving a file existed and was deleted
- Reveal the original file path, proving what was on the system
- Deletion timestamp can anchor a timeline of destruction of evidence
- Useful in data destruction investigations and anti-forensics detection

**Caveat:** Once the Recycle Bin is emptied, `$R` files are permanently deleted. However, `$I` metadata may still be recoverable via file carving or Volume Shadow Copies. The `$I` file is also deleted, but may survive in unallocated space.

### Storage Location

```
C:\$Recycle.Bin\{SID}\$I######
C:\$Recycle.Bin\{SID}\$R######
```

The `{SID}` folder corresponds to the user account that deleted the file. Map SIDs to usernames via the registry: `SOFTWARE\Microsoft\Windows NT\CurrentVersion\ProfileList`.

### Step-by-Step: Collecting Recycle Bin Artifacts

1. The `$Recycle.Bin` folder is hidden and requires elevated permissions or forensic tools to access.
2. When imaging, ensure the `$Recycle.Bin` directory is captured - it may be excluded by some collection tools if not configured properly.
3. List all SID subfolders and map them to user accounts.

### Step-by-Step: Parsing $I Files

Each `$I` file has a fixed structure:
- Bytes 0–7: Header/version
- Bytes 8–15: Original file size
- Bytes 16–23: Deletion timestamp (Windows FILETIME)
- Bytes 24+: Original file path (Unicode string)

**Using RBCmd (Eric Zimmerman):**

```bash
RBCmd.exe -d "C:\$Recycle.Bin" --csv output-folder
```

**Key output fields:**
- `FileName` - original file name
- `FilePath` - original full path of the deleted file
- `DeletedOn` - deletion timestamp
- `FileSize` - original file size

### Tools

| Tool | Type | Link |
|---|---|---|
| RBCmd | CLI | https://github.com/EricZimmerman/RBCmd |
| Autopsy (Recycle Bin module) | GUI | https://www.autopsy.com |
| FTK Imager | GUI | https://accessdata.com/solutions/digital-forensics/ftk-imager |

---

## 11. Windows Search Index

### What Is the Windows Search Index?

The Windows Search Index is a database maintained by the Windows Search service. It indexes file names, metadata, and - for certain file types like `.docx`, `.pdf`, and `.txt` - **partial file content**. This indexing happens automatically and silently in the background, often without the user's knowledge.

Forensically, this means the Search Index may contain text snippets and metadata from files that have since been deleted, as well as records of browser history that has been cleared.

### Why It Matters

**Key investigative values:**
- **Partial content recovery** - recover text fragments from deleted documents (Word, PDF, text files, etc.)
- **Browser history residue** - even if browser history was cleared, the Search Index may retain cached page titles and URLs
- **Proves file content existed** - useful when the file itself is gone
- Runs silently - users often do not know their file content is indexed

**Caveat:** The index is purged within a few days after a file is deleted. It is not a long-term repository - prioritize collection early. The database (`Windows.edb`) uses the ESE (Extensible Storage Engine) format and requires specialized tools to parse.

### Storage Location

```
C:\ProgramData\Microsoft\Search\Data\Applications\Windows\Windows.edb
```

### Step-by-Step: Collecting the Search Index

1. Copy `Windows.edb` from the path above.
2. The file is locked while the Search service is running. Use VSS (Volume Shadow Copy) or a live forensic acquisition tool to access it.
3. Also consider collecting `Windows.jfm` (journal file) for additional context.

### Step-by-Step: Parsing with LostPassword Search Index Examiner or ESEDatabaseView

**Using ESEDatabaseView (Nirsoft):**
1. Open ESEDatabaseView.
2. Load `Windows.edb`.
3. Browse tables - key tables include `SystemIndex_Gthr` and `SystemIndex_PropertyStore`.
4. Export relevant tables to CSV for analysis.

**Key fields to examine:**
- `System.ItemNameDisplay` - file name
- `System.ItemPathDisplay` - file path
- `System.ItemDate` - last modified date
- `System.Search.Contents` - indexed text content (partial)
- `System.ItemUrl` - for browser history entries, the URL

### Tools

| Tool | Type | Link |
|---|---|---|
| ESEDatabaseView | GUI | https://www.nirsoft.net/utils/ese_database_view.html |
| Search Index Examiner | GUI | https://www.lostpassword.com/search-index-examiner.htm |
| Autopsy (Keyword Search) | GUI | https://www.autopsy.com |

---

## 12. RDP Bitmap Cache

### What Is the RDP Bitmap Cache?

When a user initiates a Remote Desktop Protocol (RDP) session to another machine, Windows caches small bitmap tiles of what was displayed on the remote desktop to improve rendering performance. These tiles are stored locally on the **source machine** (the one initiating the RDP connection) and can be reconstructed into fragments of the remote desktop screen - showing what the attacker or user saw during the session.

### Why It Matters

**Key investigative values:**
- Reconstruct partial screenshots of RDP sessions - what was displayed on the remote machine
- Prove lateral movement occurred between hosts
- Reveal what applications, files, or data were visible during a remote session
- Particularly valuable when the remote machine logs are unavailable or have been tampered with

**Caveat:** The cache stores tiles, not full screenshots. Reconstruction produces fragmentary images that may be incomplete. The quality and completeness depends on session activity and cache size.

### Storage Location

```
C:\Users\<username>\AppData\Local\Microsoft\Terminal Server Client\Cache\
```

Files are named: `bcache##.bmc` and `cache####.bin`

### Step-by-Step: Parsing RDP Bitmap Cache

**Using BMC-Tools:**

```bash
python bmc-tools.py -s Cache0000.bin -d output-folder
```

This extracts individual bitmap tiles to the output folder.

**Using RDPCacheStitcher (GUI):**
1. Open RDPCacheStitcher.
2. Load the `.bmc` or `.bin` cache file.
3. Use the stitching feature to arrange tiles into recognizable screen fragments.
4. Export reconstructed images for documentation.

### Tools

| Tool | Type | Link |
|---|---|---|
| bmc-tools | CLI (Python) | https://github.com/ANSSI-FR/bmc-tools |
| RDPCacheStitcher | GUI | https://github.com/BSI-Bund/RdpCacheStitcher |

### Investigative Correlation

- **Event Logs (ID 4624 Type 10)** - confirm the RDP logon event
- **Event Logs (ID 4778 / 4779)** - session reconnect/disconnect events
- **SRUM** - identify remote desktop client application activity and duration
- **Prefetch** - confirm `mstsc.exe` (RDP client) execution times

---

## 13. ThumbCache

### What Is ThumbCache?

ThumbCache is a collection of database files maintained by Windows Explorer to store thumbnail previews of images, videos, documents, and other visual content. When a user browses a folder in thumbnail view, Windows generates and caches these previews.

Forensically, ThumbCache is valuable because the thumbnail often **persists after the source file is deleted**, proving that a file existed and was viewed by the user.

### Why It Matters

**Key investigative values:**
- Prove a file existed on the system and was rendered as a thumbnail (viewed in Explorer)
- Recover thumbnail-size previews of deleted images, documents, and videos
- Can reveal content (e.g., image content) even without the original file
- Each entry has a timestamp indicating when the thumbnail was generated

**Caveat:** Thumbnails are low-resolution previews, not full copies of the original file. The presence of a thumbnail proves the file was present and viewed in Explorer, but does not prove the user opened the file itself.

### Storage Location

```
C:\Users\<username>\AppData\Local\Microsoft\Windows\Explorer\
```

Files: `Thumbcache_32.db`, `Thumbcache_96.db`, `Thumbcache_256.db`, `Thumbcache_1024.db`, `Thumbcache_sr.db`, `iconcache_*.db`

Different database files correspond to different thumbnail sizes. The `sr.db` file is for the Start screen/taskbar thumbnails.

### Step-by-Step: Parsing ThumbCache

**Using Thumbcache Viewer:**
1. Open Thumbcache Viewer.
2. Load the `.db` file from the Explorer folder.
3. Browse thumbnails - each is associated with a cache entry ID.
4. Export thumbnails of interest as image files.

**Mapping thumbnails to file paths:**
- ThumbCache entries contain a hash of the file path, not the path itself.
- Use the `Windows.edb` Search Index or shellbags to cross-reference hashes to actual file paths.

### Tools

| Tool | Type | Link |
|---|---|---|
| Thumbcache Viewer | GUI | https://thumbcacheviewer.github.io/ |
| Autopsy (Thumbnail module) | GUI | https://www.autopsy.com |

---

## 14. Artifact Correlation: Building a Timeline

The real investigative power comes from correlating multiple artifacts together. No single artifact proves a complete story - but combined, they build a defensible, chronological account of events.

### Timeline Construction Methodology

1. **Establish a baseline** - determine normal system activity from log patterns
2. **Identify anchor events** - find high-confidence timestamps from multiple artifact sources
3. **Expand outward** - use anchor events to contextualize surrounding activity
4. **Cross-validate** - confirm each finding with at least one corroborating artifact
5. **Document gaps** - note where logs were cleared, artifacts are absent, or retention windows have expired

### Correlation Map

```
User opens malicious file:
  - LNK file created (access timestamp)
  - RecentDocs / MRU updated (file name + extension recorded)
  - Jump List entry created (application + file path + timestamp)
  
Malware executes:
  - Prefetch file created or updated (.pf file, executable name + run count)
  - SRUM records application network usage (bytes sent to C2)
  - Event Log 4688 (if process auditing enabled)
  - Event Log 7045 (if service installed for persistence)
  
Attacker accesses another host via RDP:
  - Event Log 4624 Type 10 on target host
  - RDP Bitmap Cache tiles created on source host
  - Prefetch: mstsc.exe execution
  - SRUM: mstsc.exe network activity
  
User attempts to destroy evidence:
  - Files moved to Recycle Bin - $I files created (deletion timestamps)
  - Event Log 1102: Security log cleared
  - ThumbCache may retain thumbnail previews of deleted files
  - LNK files / Jump Lists may persist for files now deleted
```

### Common Investigation Scenarios

| Scenario | Primary Artifacts | Supporting Artifacts |
|---|---|---|
| **Insider data theft** | Jump Lists, LNK, RecentDocs, SRUM | Prefetch, Recycle Bin, USB artifacts |
| **Malware execution** | Prefetch, Event Logs (4688, 7045), SRUM | Jump Lists, Registry Run keys |
| **Lateral movement via RDP** | Event Logs (4624 Type 10), RDP Cache | SRUM, Prefetch (mstsc.exe) |
| **Ransomware investigation** | Prefetch, Event Logs, SRUM | Jump Lists, ThumbCache, VSS |
| **User activity reconstruction** | Jump Lists, LNK, RecentDocs, MRU | SRUM, Search Index, ThumbCache |
| **Evidence destruction** | Recycle Bin ($I), Event Log 1102 | LNK files, ThumbCache, VSS |

---

## 15. Forensic Tooling Overview

### Eric Zimmerman Tools (Free, Windows)
A comprehensive suite of CLI and GUI tools for parsing Windows forensic artifacts. Widely used and well-maintained.

| Tool | Artifact | Link |
|---|---|---|
| JLECmd | Jump Lists | https://github.com/EricZimmerman/JLECmd |
| LECmd | LNK Files | https://github.com/EricZimmerman/LECmd |
| PECmd | Prefetch | https://github.com/EricZimmerman/PECmd |
| EvtxECmd | Event Logs | https://github.com/EricZimmerman/evtx |
| SRUMECmd | SRUM | https://github.com/EricZimmerman/Srum |
| RBCmd | Recycle Bin | https://github.com/EricZimmerman/RBCmd |
| RECmd | Registry / MRU | https://github.com/EricZimmerman/RECmd |
| Registry Explorer | Registry (GUI) | https://ericzimmerman.github.io/#!index.md |
| JumpList Explorer | Jump Lists (GUI) | https://ericzimmerman.github.io/#!index.md |

### KAPE (Kroll Artifact Parser and Extractor)
A powerful collection and processing framework that can triage a live or imaged system, collecting and parsing artifacts in one workflow.
- https://www.kroll.com/en/services/cyber/incident-response-recovery/kroll-artifact-parser-and-extractor-kape

### Velociraptor
An open-source endpoint visibility and DFIR platform that can collect and parse artifacts at scale across many endpoints simultaneously.
- https://www.velociraptor.app/

### Autopsy
A free, open-source GUI forensic platform built on The Sleuth Kit. Good for disk image analysis, file recovery, and artifact review.
- https://www.autopsy.com/

### Nirsoft Utilities
Free, portable utilities for specific artifact types.
- **ESEDatabaseView** (SRUM, Search Index): https://www.nirsoft.net/utils/ese_database_view.html
- **WinPrefetchView** (Prefetch): https://www.nirsoft.net/utils/win_prefetch_view.html

### TZWorks
Commercial tools with civilian and government licensing for precise, documented artifact parsing.
- https://tzworks.com/

---

## 16. Investigative Scenarios & Case Examples

### Scenario 1: Insider Data Exfiltration

**Situation:** A departing employee is suspected of copying confidential client files to a personal USB drive before resigning.

**Investigative approach:**

1. **Jump Lists** - examine AutomaticDestinations for the user's account. Look for access to files on drive letters associated with removable media (e.g., `E:\`, `F:\`).
2. **LNK Files** - parse LNK files in the user's Recent folder. Look for `VolumeSerialNumber` values that do not match the internal drive. Match these against any seized USB devices.
3. **RecentDocs / MRU** - confirm files of the relevant extension (e.g., `.pdf`, `.xlsx`) were accessed, and in what order.
4. **SRUM** - check network usage. If the employee emailed files or used a cloud sync tool (Dropbox, OneDrive), SRUM will show bytes sent by that application.
5. **Prefetch** - look for execution of tools like robocopy, xcopy, or third-party transfer tools.
6. **Recycle Bin** - check if the employee deleted files before leaving. `$I` files reveal what was deleted and when.

---

### Scenario 2: Ransomware Incident

**Situation:** A ransomware attack encrypted files across a network. The initial infected host needs to be identified.

**Investigative approach:**

1. **Prefetch** - search for the ransomware binary name (e.g., `encryptor.exe`). The PF file creation time gives an approximate first execution time.
2. **Event Logs** - look for Event ID 7045 (service installation) and 4688 (process creation) around the execution time. Check for account used.
3. **SRUM** - quantify how much data the ransomware process sent over the network. Identify the C2 IP if exfiltration preceded encryption.
4. **Jump Lists / LNK** - if the ransomware was delivered as a document (e.g., malicious macro), LNK and Jump List artifacts will show the user opening the lure file.
5. **ThumbCache** - if document previews were cached, they may reveal the content of the lure document.

---

### Scenario 3: Lateral Movement via RDP

**Situation:** An attacker compromised one Windows host and used it to RDP into other internal machines.

**Investigative approach:**

1. **Event Logs (Security, Event ID 4624 Type 10)** - on destination hosts, identify RDP logon events. Note the source IP and username.
2. **Prefetch on source host** - confirm `mstsc.exe` (Remote Desktop client) executed and when.
3. **RDP Bitmap Cache on source host** - reconstruct what the attacker saw on the remote desktop during the session.
4. **SRUM on source host** - confirm `mstsc.exe` network activity duration and volume.
5. **Event Logs (4778 / 4779)** - session connect/disconnect timestamps on the target host to establish session duration.

---

## 17. References and Resources

### Primary Reference - This Playbook's Foundation

- **SANS DFIR - Windows Forensic Analysis Playbook v2** - https://www.sans.org/posters/windows-forensic-analysis-playbook

### Tooling Documentation and Repositories

- **Eric Zimmerman's Tools** - https://ericzimmerman.github.io/#!index.md
- **KAPE (Kroll Artifact Parser and Extractor)** - https://www.kroll.com/en/services/cyber/incident-response-recovery/kroll-artifact-parser-and-extractor-kape
- **Velociraptor DFIR Platform** - https://www.velociraptor.app/ | https://docs.velociraptor.app/
- **Autopsy Digital Forensics** - https://www.autopsy.com/
- **Nirsoft Utilities** - https://www.nirsoft.net/
- **TZWorks Forensic Tools** - https://tzworks.com/
- **bmc-tools (RDP Cache)** - https://github.com/ANSSI-FR/bmc-tools
- **RDPCacheStitcher** - https://github.com/BSI-Bund/RdpCacheStitcher
- **Chainsaw (Event Log hunting)** - https://github.com/WithSecureLabs/chainsaw
- **Thumbcache Viewer** - https://thumbcacheviewer.github.io/

### Windows Documentation

- **Windows Event Log Event IDs Reference** - https://docs.microsoft.com/en-us/windows/security/threat-protection/auditing/
- **MITRE ATT&CK Framework (Windows)** - https://attack.mitre.org/matrices/enterprise/windows/

---
