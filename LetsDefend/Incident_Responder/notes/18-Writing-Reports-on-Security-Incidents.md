# Writing Security Incident Reports
> "Notes" are the first parts, a "template" is further down

## Purpose of an Incident Report

An incident report should:
- Clearly explain what happened.
- Document the investigation process.
- Record evidence and findings.
- Recommend remediation and prevention steps.
- Allow future analysts to reproduce the investigation.

The report should be easy to read regardless of the audience.

---

# Reporting Principles

## 1. Intelligibility
Write for your audience.

- Executives need business impact and summary.
- SOC analysts need technical details.
- Separate technical content from the Executive Summary.

---

## 2. Timeline
Present events in chronological order.

Include:
- When the attack started
- What the attacker did
- What defenders observed
- Actions taken by the SOC
- Containment and recovery

A good timeline allows readers to quickly understand the entire incident.

---

## 3. Repeatability
Document every investigation step clearly.

Anyone reading the report should be able to:
- Follow the same investigation
- Validate findings
- Repeat the process during future incidents

Always document:
- Tools used
- Commands executed
- Detection methods
- Evidence collected

---

## 4. Stay Focused
Only include information relevant to the incident.

Avoid unrelated discussions.

---

# Writing Style

## Use Past Tense

Describe events that already happened.

Example:
- The attacker executed Mimikatz.
- The firewall blocked outbound traffic.

---

## Keep Sentences Short

Avoid unnecessary words.

Poor:
> An unknown hacker attempted to attack our favorite server.

Better:
> An unknown attacker attempted SQL Injection against the web server.

---

## Include Important Details

Always answer:

- What happened?
- Where?
- Which host?
- Which IP?
- Which file?
- Which user?
- Which IOC?

Poor:
> Malware was detected.

Better:
> Mimikatz.exe was detected on WEB-01 (192.168.10.15).

---

## Report What You Did

Focus on completed actions.

If something wasn't performed, only explain it if relevant.

Example:
> Memory analysis could not be performed because the attacker rebooted the server.

---

## Define Abbreviations

The first occurrence should include the full meaning.

Example:

Indicator of Compromise (IOC)

Endpoint Detection and Response (EDR)

Security Information and Event Management (SIEM)

---

## Stay Consistent

Use the same terminology throughout.

Example:

Choose **Host**

Don't switch between:
- Host
- Endpoint
- Node
- System

unless necessary.

---

# Formatting Standards

## Date & Time

Use one format throughout the report.

Example:

2026-07-13 14:32 UTC

Don't mix:
- July 13, 2026
- 13/07/2026
- 07-13-2026

---

## Fonts

Use professional fonts.

Examples:
- Arial
- Calibri
- Verdana

---

## Tables

Tables improve readability.

Use tables for:
- Hosts
- IOCs
- Users
- Malware
- Evidence
- Timeline

---

## Data Consistency

If one IOC contains:

- SHA256
- File path
- Host
- Timestamp

Every IOC should contain the same fields.

---

## Lists

Use bullet or numbered lists for:

- Recommendations
- Malware
- Hosts
- Affected assets

---

# Standard Incident Report Structure

1. Executive Summary
2. Table of Contents
3. Incident Background
4. Findings
5. Timeline
6. Recommendations
7. Appendix

---

# Executive Summary

High-level overview.

Include:
- What happened
- Business impact
- Severity
- Current status

Avoid technical jargon.

---

# Incident Background

Describe:
- How the incident was detected
- Initial alert
- Scope
- Investigation overview

---

# Findings

Document everything discovered.

Examples:
- Initial Access
- Malware
- Persistence
- Lateral Movement
- C2 Communication
- Exfiltration
- Evidence
- IOCs

---

# Timeline

List only significant events.

Include:
- Timestamp
- Event
- Action taken

---

# Recommendations

Separate into:

## Immediate
- Isolate host
- Reset passwords
- Block IOCs
- Patch systems

## Long-term
- Deploy EDR
- Improve logging
- Enable MFA
- User awareness training
- Detection engineering improvements

---

# Appendices

Place large datasets here.

Examples:
- IOC list
- SHA256 hashes
- PCAP files
- Log excerpts
- YARA rules
- Screenshots



---


# Security Incident Report

**Incident ID:** IR-2026-001

**Incident Title:** Credential Theft via Phishing Email

**Severity:** High

**Status:** Closed

**Report Author:** Prince Lassey

**Date:** 20255-04-03

---

# Executive Summary

A phishing email successfully compromised one employee account through credential theft.

The attacker authenticated from an unusual IP address and attempted to access Microsoft 365 resources. Multi-Factor Authentication prevented further compromise.

The affected account was disabled, credentials were reset, and malicious IP addresses were blocked.

No evidence of data exfiltration was identified.

---

# Table of Contents

1. Executive Summary
2. Incident Background
3. Findings
4. Timeline
5. Recommendations
6. Appendix

---

# Incident Background

## Detection

The incident was detected by Microsoft Defender after multiple suspicious login attempts from an unusual geographic location.

The SIEM generated an alert for Impossible Travel.

The SOC analyst validated the alert and opened an investigation.

---

# Findings

## Initial Access

The attacker obtained valid credentials through a phishing email.

Affected User:

| Username | Department |
|-----------|------------|
| jsmith | Finance |

---

## Affected Assets

| Host | IP Address |
|-------|------------|
| FIN-LAPTOP-03 | 10.10.15.42 |

---

## Indicators of Compromise (IOCs)

### IP Addresses

| IP | Description |
|----|-------------|
| 185.xxx.xxx.xxx | Malicious Login Source |

### Domains

| Domain |
|---------|
| login-office365-support.com |

### File Hashes

| SHA256 | Filename |
|---------|----------|
| abcd1234... | invoice.pdf.exe |

---

## Evidence

Evidence collected included:

- Microsoft Defender alerts
- Azure AD sign-in logs
- Email headers
- Proxy logs
- Endpoint telemetry

---

## Investigation

The investigation confirmed:

- Successful credential theft
- Failed MFA bypass attempts
- No malware execution
- No lateral movement
- No privilege escalation
- No data exfiltration

---

# Timeline

| Time (UTC) | Event |
|------------|-------|
| 08:11 | Phishing email delivered |
| 08:18 | User submitted credentials |
| 08:20 | Suspicious login detected |
| 08:22 | SIEM generated alert |
| 08:28 | Analyst started investigation |
| 08:35 | User account disabled |
| 08:42 | Password reset |
| 08:50 | Malicious IP blocked |
| 09:10 | Investigation completed |
| 09:20 | Incident closed |

---

# Containment

- Disabled affected account
- Blocked malicious IP
- Removed phishing email
- Forced password reset
- Revoked active sessions

---

# Eradication

- Removed malicious emails
- Verified endpoint integrity
- Updated detection rules

---

# Recovery

- Restored user account
- Re-enabled access after password reset
- Verified normal authentication activity

---

# Root Cause

The user entered credentials into a fraudulent Microsoft login page after clicking a phishing email.

---

# Impact

Business Impact:
- One user account compromised

Operational Impact:
- No service interruption

Data Impact:
- No confirmed data loss

---

# Recommendations

## Immediate

- Reset passwords
- Review mailbox rules
- Block phishing domain
- Monitor affected account

## Long-Term

- Enforce phishing-resistant MFA
- Conduct phishing awareness training
- Improve email filtering
- Implement Conditional Access policies
- Add new SIEM detection rules

---

# Lessons Learned

- Users require additional phishing awareness.
- Existing MFA successfully prevented account takeover.
- Earlier detection reduced response time.
- Similar phishing domains should be proactively blocked.

---

# Appendix

## IOC List

### Domains

- login-office365-support.com

### IP Addresses

- 185.xxx.xxx.xxx

### SHA256

- abcd1234...

## Screenshots

- Microsoft Defender Alert
- SIEM Timeline
- Email Header Analysis

## References

- MITRE ATT&CK Techniques
- Detection Rules
- Case Notes