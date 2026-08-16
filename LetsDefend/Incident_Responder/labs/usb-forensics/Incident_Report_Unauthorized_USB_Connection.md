# Incident Report: Unauthorized USB Device Connection and Covert Remote Access Tool Execution

**Analyst:** Prince Lassey

**Date of Report:** August 2, 2026.

---

## 1. Executive Summary

On **November 13, 2023**, a USB storage device was connected to the subject endpoint. Forensic analysis of registry artifacts, Windows event logs, shellbags, and jumplists established that within approximately **52 seconds** of the device being connected, the user navigated directly to a suspiciously named directory on the device (`c2initialentry`) and executed a binary (`Entry_fix21.exe`) later confirmed by the SOC team to be a **renamed installation of AnyDesk**, a legitimate Remote Monitoring and Management (RMM) tool.

The speed and precision of this activity from device connection to targeted folder access to tool execution, all within under a minute and combined with the deliberately renamed binary and the suspicious folder naming convention, is consistent with a **pre-staged, deliberate action** rather than incidental or exploratory USB usage. The use of a renamed, legitimate RMM tool is a well-documented technique used to establish covert remote access while evading signature-based detection, as legitimate tools are less likely to be flagged by antivirus/EDR solutions than custom malware.

This report documents the full forensic timeline, the artifacts and methodology used to establish it, and recommendations for containment and policy response.

---

## 2. Scope and Objectives

**Scope:** Forensic analysis of the subject Windows endpoint's registry hives (SYSTEM, NTUSER.DAT, USRCLASS.DAT) and native Windows event logs to determine:
- Whether an external storage device was connected to the system
- The identity and characteristics of that device
- What data/folders were accessed on the device
- What actions (if any) were taken using files or tools from the device

**Objective:** Establish a defensible, timestamp-corroborated timeline of USB device activity to determine whether the connection and subsequent actions constitute a security incident warranting further investigation or disciplinary/legal action.

---

## 3. Evidence Sources

| Source | Location | Purpose |
|---|---|---|
| SYSTEM registry hive | `HKLM\SYSTEM\CurrentControlSet\Enum\USBSTOR` | Device identification, connect/disconnect timestamps |
| Windows Event Logs | Partition (1006), Kernel-PnP (400/410), NTFS Operational (142) | Independent corroboration of device connection, drive letter assignment |
| USRCLASS.DAT (Shellbags) | `Local Settings\Software\Microsoft\Windows\Shell\Bags` / `BagMRU` | Folder access history on the USB device |
| Jumplists (Custom/Automatic Destinations) | `%USERPROFILE%\AppData\Roaming\Microsoft\Windows\Recent\` | File/application execution evidence tied to the USB device |

---

## 4. Forensic Timeline

All timestamps below are corroborated across at least two independent artifact sources, in line with forensic best practice of not relying on a single data point.

| Time (UTC) | Event | Source Artifact(s) | Corroboration |
|---|---|---|---|
| **08:32:23** | USB storage device connected to the system | USBSTOR registry key (`0064` value); Partition log (Event ID 1006) | Registry timestamp matches Partition log event time exactly |
| **08:32:23.69** | USB device drivers configured (Kernel-PnP) | Kernel-PnP log, Event ID 400 | Device Name and Class GUID (`{4d36e967-e325-11ce-bfc1-08002be10318}`) match the USBSTOR entry |
| **08:32:25** *(approx.)* | Drive letter **E:** assigned to the device | NTFS Operational log, Event ID 142 | Confirms subsequent `E:\` file paths belong to this specific device |
| **08:32:41** | Directory **`c2initialentry`** accessed on `E:\` drive | Shellbags (USRCLASS.DAT) | Timestamp falls within 18 seconds of device connection - consistent with direct, targeted navigation rather than casual browsing |
| **08:33:15** | Binary **`Entry_fix21.exe`** executed from the USB device | Jumplist (Custom Destinations) | Confirmed by SOC as a renamed installation of **AnyDesk** (legitimate RMM software) |

**Total elapsed time from device connection to tool execution: approximately 52 seconds.**

---

## 5. Analysis

### 5.1 Device Identification
The connected device was uniquely identified via its Windows-assigned serial number and Class GUID (`{4d36e967-e325-11ce-bfc1-08002be10318}`), both of which were cross-verified across the USBSTOR registry key and the Kernel-PnP event log, providing high confidence that all subsequent artifacts (shellbags, jumplist entries under `E:\`) can be reliably attributed to this specific physical device.

### 5.2 Behavioral Assessment
The 18-second gap between device connection and folder access, followed by tool execution roughly 34 seconds later, suggests the user had **prior knowledge** of the exact folder and file location on the device - there is no evidence in the shellbag data of exploratory browsing through multiple folders before reaching `c2initialentry`. This pattern is more consistent with a rehearsed or pre-planned action than with a user casually inspecting an unfamiliar drive.

### 5.3 Significance of the Renamed RMM Tool
Renaming a legitimate, digitally-signed tool such as AnyDesk (in this case to `Entry_fix21.exe`) is a recognized technique for:
- Evading application whitelisting rules that may key off of the original filename
- Reducing suspicion from users or IT staff who might notice "AnyDesk.exe" in a process list but overlook a generically named file
- Potentially evading signature/heuristic detections tuned to expect the tool's default naming

### 5.4 Alternative Explanations Considered
In the interest of a balanced analysis, it should be noted that RMM tools such as AnyDesk are also legitimately used for remote IT support and administration. It is possible - though less likely given the folder naming and the speed of execution - that this represents an authorized remote-support session using a personal/portable AnyDesk installation rather than official company deployment channels. This alternative explanation should be put to the individual involved as part of any follow-up interview, consistent with fair investigative practice.

---

## 6. Indicators of Compromise (IOCs)

| Type | Value |
|---|---|
| Device Class GUID | `{4d36e967-e325-11ce-bfc1-08002be10318}` |
| Suspicious folder name | `c2initialentry` |
| Renamed binary | `Entry_fix21.exe` (confirmed AnyDesk installer/executable) |
| Assigned drive letter | `E:` |

---

## 7. Recommendations

1. **Interview the device owner/user** to obtain an explanation for the folder naming and tool execution, referencing the alternative explanation noted in Section 5.4.
2. **Preserve the physical USB device** if still available, and image it separately for a full content review (this report was scoped to host-side artifacts only).
3. **Review endpoint policy** regarding removable media - consider whether USB device control/whitelisting is enforced, and if not, evaluate implementing it.
4. **Audit for AnyDesk (or other RMM tool) usage** organization-wide via EDR/process execution logs to determine whether this is an isolated event or part of a broader pattern.
5. **Correlate network logs** for the affected host around and after 08:33:15 UTC to determine whether an outbound remote-access session was actually established following execution, and if so, to what destination.
6. **Consider containment** of the endpoint pending interview outcomes, particularly if network correlation confirms an active remote session was established.

---

## 8. Conclusion

This investigation successfully reconstructed a precise, multi-source-corroborated timeline of USB device activity on the subject endpoint. The evidence indicates a rapid, targeted sequence of folder access and execution of a renamed remote-access tool, occurring within under a minute of the device being connected. While a legitimate explanation cannot be ruled out, the totality of the evidence - timing, folder naming, and tool renaming - supports escalating this finding for further investigation, including a formal interview with the individual involved and correlation with network-level evidence.

---
