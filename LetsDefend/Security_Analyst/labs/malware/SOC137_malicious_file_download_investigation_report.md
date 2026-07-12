# SOC Case Investigation Report – Malicious File or Script Download Attempt

**Platform:** [LetsDefend](https://letsdefend.io)

**Date:** 2025-08-05  
**Alert ID:** 76  
**Alert Rule Name:** `SOC137 - Malicious File/Script Download Attempt`

**Severity:** Medium  
**Category:** Malware

---

## Overview

This investigation involved a medium-severity alert triggered by a suspicious `.docm` file attempting to download and execute a PowerShell-based payload. I escalated the incident to a case, investigated through multiple tools (VirusTotal, log management, endpoint security), and concluded as a **True Positive**. A playbook was completed and the endpoint isolated.

---

## Initial Alert Details

- **Event ID:** 76  
- **Alert Rule:** SOC137 – Malicious File/Script Download Attempt  
- **Source IP:** `172.16.17.37`  
- **Source Hostname:** `NicolasPRD`  
- **File Type:** `.docm`  
- **File Hash:** `f2d0c66b801244c059f636d08a474079`  
- **File Name:** `INVOICE PACKAGE LINK TO DOWNLOAD.docm`  

> 

---

## Investigation Steps

### 1. Case Creation
- From the **Main Channel** of the monitoring page, I took ownership of the event and it was moved to the **Investigation Channel**
- Alert was escalated from the **Investigation Channel** on the **Monitoring Page** to the **Case Management** panel.
- Event ID 76 was used to initiate a case for deeper investigation.

---

### 2. Alert Analysis
- **Threat Indicator Selected:** Unknown or unexpected outgoing internet traffic.
- Opened the alert and inspected metadata, including:
  - Source IP and hostname
  - I copied the file hash and run it through [VirusTotal](https://virustotal.com)
  
> 

- **VirusTotal Result:**  
  - **38/66** security vendors flagged the file as malicious.
  - Macro code (`auto_open()`) embedded in `NewMacros.bas` module.
  - Executes obfuscated PowerShell script to download and run payload from a suspicious URL.

---

### 3. Endpoint Containment
- Searched for affected device in **Endpoint Security**.
- Device successfully **contained** to stop lateral movement.
- Checked:
  - **Terminal history** – included PowerShell commands
  - **Browser history** – checked suspicious URLs visited

> 

---

### 4. Log Management Analysis
- Queried logs using:
  - **Source IP:** `172.16.17.37`
  - Returned 3 events (all proxy-related).

#### Event Connections:
- **1st & 2nd Event:**
  - Destination IP: `49.51.12.195`
  - **VirusTotal:** Clean  
  - **Geolocation:** Canada

- **3rd Event:**
  - Destination IP: `31.214.157.60`
  - **VirusTotal:** 2 vendors flagged as **malicious**  
  - **Geolocation:** Netherlands  
  - Categories: Phishing, Malicious, Suspicious (BitDefender, AlphaMountain.ai)

> 

---

### 5. Root Cause & Malicious URL

- **Suspected URL:** `[http://ueba6ka.club.images.dvein.eu/.../payload.evi](http://ueba6ka.club/images/DVeUkINudhi79z0c_2Bv/hcMjSPUhHNICgDZ2eJc/uPkHWXvBmVjikkuyor3cnx/gi3BCr71LrlRP/OOmOS8_2/Bl7A_2Fjz2BM4Pth4RZbBKn/1OVxs19bE9/6wtE2QLgVO1DP1TCF/SoIEOJUXIYbo/RtuJbNDFWW5/V.avi)`  
- **VirusTotal Result:**
  - **7 security vendors** flagged as **malicious**
  - Categorized as:
    - Malware
    - P***
    - Spyware
   

> 

## Conclusion
- True positive
The macro-enabled Word file initiated a download from a known malicious domain, which led to alert generation.

- Alert closed in Investigation Channel after determining it as a True Positive.
- Device isolated to protect internal network.
- Playbook completed, and case documented here on my Github.
- Badge Unlocked: First Blood – LetsDefend

---

## Playbook Execution

1. **Threat Indicator:** Unknown outbound traffic  
2. **Malware Quarantined?** Yes, endpoint was isolated.  
3. **Malware Analysis:** VirusTotal flagged both hash and destination IP as malicious  
4. **C2 Address Accessed?**  
   - Only one host communicated with the C2
   - Selected: **Not Accessed** for broader organization  
5. **Artifacts Added:**
   - Type: Hash (MD5)  
   - Value: `f2d0c66b801244c059f636d08a474079`  
   - Comment: `Malicious File/Script Download Attempt`

6. **Analyst Notes:**

From `hispasec` security vendor,
The macro code provided is indeed malicious. It contains a subroutine named AutoOpen(), which is automatically executed when the document is opened. This is a common technique used by malware to initiate its malicious activities without user interaction.

The AutoOpen() subroutine uses the Shell function to execute a PowerShell command. The PowerShell command is obfuscated, but upon closer inspection, it appears to be creating a new .NET WebClient object and invoking its DownloadString method. This method downloads a file from a specified URL ('https://filetransfer.io/data-package/UR2whuBv/download') and executes it.

This behavior is highly suspicious as it's a common tactic used by malware to download and execute additional payloads from the internet. These payloads can contain any type of malicious software, such as ransomware, spyware, or trojans.

Furthermore, the use of obfuscation in the PowerShell command is another strong indicator of malicious intent. Obfuscation is often used by malware authors to hide their code's true purpose and evade detection by security software.

In conclusion, this macro exhibits several signs of malicious behavior, including automatic execution upon document opening, downloading and executing files from the internet, and code obfuscation.

Or in more simple terms, the document was a trap. It had secret instructions that told the computer to download and run something harmful from the internet, all without warning the user. This is a clear example of malware behavior.

# Reflection
This investigation reinforced the importance of not trusting seemingly harmless files, especially those received via email or from unverified sources which can serve as silent weapons, capable of downloading and executing malicious code without user interaction.
By walking through the full incident response process, thus from alert detection, hash analysis, endpoint containment, log correlation, to playbook execution, I gained hands-on experience in threat hunting, incident classification, and malware behavior analysis.

Some points I'll note down for later referencing:
- Early detection and containment are critical to preventing the spread of malware.
- Even if an IP or domain seems clean, verification matters.
- Playbooks streamline investigation and improve response speed and consistency.
- Using simple tools like VirusTotal, combined with critical thinking, can uncover the full story behind an alert.

This case not only added to my true-positive investigations but also deepened my confidence in operating within a SOC environment.
