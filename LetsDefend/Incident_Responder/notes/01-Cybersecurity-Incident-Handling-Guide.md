# Cybersecurity Incident Handling (NIST SP 800-61)

## Overview

Even organizations with mature security programs will eventually experience security incidents. The goal of Incident Response (IR) is **not to prevent every attack**, but to detect, contain, eradicate, recover, and learn from incidents while minimizing business impact.

NIST defines Incident Response as a continuous lifecycle consisting of four phases:

1. Preparation
2. Detection & Analysis
3. Containment, Eradication & Recovery
4. Post-Incident Activity

Each incident should strengthen the organization's ability to respond to future incidents.

---

# 1. Preparation

Preparation happens **before** an incident occurs. Everything that enables faster, smoother, and more effective incident response belongs in this phase.

A strong preparation phase greatly reduces response time and mistakes during an actual incident.

## Important Areas

| Area | Purpose |
|------|---------|
| Communication | Maintain contact lists, alternative communication channels, and a war room. |
| Asset Inventory | Keep an up-to-date inventory of servers, endpoints, applications, and owners. |
| Documentation | Prepare IR policies, playbooks, procedures, and report templates. |
| Network Topology | Maintain accurate network diagrams to understand attack paths. |
| IR Tools | Prepare forensic workstations, removable media, laptops, and forensic software. |
| Preventive Security | Deploy EDR, IDS/IPS, AV, Firewall, WAF, DLP, and conduct security awareness training. |

### NIST recommends organizations establish:

- Incident Response Policy
- Incident Response Plan
- Reporting Procedures
- Communication Guidelines
- Defined IR Team Structure
- Internal and External Communication Channels
- Incident Response Training
- Tabletop Exercises

> **Key takeaway:** Preparation determines how effective the entire incident response process will be.

---

# 2. Detection & Analysis

This phase begins when suspicious activity is detected.

Detections may originate from:

- SIEM
- EDR
- IDS/IPS
- Firewall alerts
- IT administrators
- Employees
- External notifications

---

## Verify Before Acting

A detection **does not automatically mean a security incident.**

Always verify:

- Is the alert accurate?
- Is it malicious?
- Is it a false positive?
- What evidence supports it?

### Example

An unusual file extension does **not** automatically indicate ransomware.

Possible explanations include:

- User error
- Administrator activity
- Legitimate software
- Malware

Always investigate before performing disruptive actions.

---

## Record the Incident

Once confirmed, begin documenting immediately.

Incident records should include:

- Incident status
- Summary
- Indicators of Compromise (IOCs)
- Related incidents
- Actions taken
- Evidence collected
- Chain of custody (if applicable)
- Impact assessment
- Contact information
- Analyst notes
- Next steps

Proper documentation is critical for investigations and future reporting.

---

## Prioritize the Incident

When multiple incidents occur, prioritize based on business impact rather than arrival time.

Common prioritization factors include:

- Severity
- Criticality
- Business impact
- Recoverability
- Scope
- Sensitive information involved

### Functional Impact

| Level | Description |
|------|-------------|
| None | No service disruption. |
| Low | Services continue with reduced efficiency. |
| Medium | Some users lose access to critical services. |
| High | Critical services become unavailable to all users. |

### Information Impact

| Category | Description |
|-----------|-------------|
| None | No data was compromised. |
| Privacy Breach | Personally Identifiable Information (PII) was exposed. |
| Proprietary Breach | Confidential business information was exposed. |
| Integrity Loss | Information was modified or deleted. |

### Recoverability

Recovery effort is also considered during prioritization.

Generally classified as:

- Regular Recovery
- Supplemented Recovery
- Extended Recovery
- Not Recoverable

---

## Notify the Right People

After confirming the incident:

Notify the appropriate stakeholders.

Examples include:

- SOC Manager
- System Owner
- IT Team
- Legal
- HR
- Executive Management
- Law Enforcement (if necessary)

Preparation should already define **who** is notified and **how**.

---

## Analyze the Incident

Analysis aims to understand:

- Initial access
- Attacker's actions
- Affected systems
- Persistence mechanisms
- Lateral movement
- Data accessed or exfiltrated

### Root Cause Comes First

Always identify and eliminate the attacker's initial access before removing malware.

Example:

```text
Attacker exploits vulnerable web application
                ↓
         Installs malware
```

If the malware is removed first while the vulnerability remains:

```text
Attacker simply exploits the same vulnerability again.
```

Always remove the root cause before performing complete cleanup.

---

# 3. Containment, Eradication & Recovery

These activities are closely connected.

---

## Containment

The goal is to stop the attacker from causing additional damage.

Containment should happen **as soon as the incident is confirmed.**

Common containment actions include:

- Disconnect affected host
- Disable compromised accounts
- Stop affected services
- Isolate systems using VLANs
- EDR Network Isolation
- Block malicious IPs or domains

For critical servers, predefined containment procedures are recommended because immediate shutdown may severely impact business operations.

---

## Eradication

Once contained, remove everything the attacker left behind.

Examples:

- Delete malware
- Remove persistence mechanisms
- Disable compromised accounts
- Remove attacker-created accounts
- Kill malicious processes
- Patch exploited vulnerabilities

> **Important:** Always preserve evidence before deleting artifacts.

Examples of evidence:

- File hashes
- Malware samples
- Screenshots
- Log files

Evidence first. Cleanup second.

---

## Recovery

Recovery restores systems to normal operations.

Common recovery activities include:

- Restore from backup
- Rebuild compromised systems
- Replace malicious files
- Install patches
- Reset passwords
- Harden configurations
- Return services to production

Recovery should begin only after confirming the attacker no longer has access.

---

# 4. Post-Incident Activity

Incident response does not end when systems are restored.

This phase focuses on improving future incident response.

---

## Lessons Learned

A post-incident review should answer questions such as:

- What happened?
- When did it happen?
- Were procedures followed?
- What slowed the response?
- What information was missing?
- What should be improved?
- Which indicators should be monitored in the future?
- Which tools or resources are needed?

Every incident should improve future readiness.

---

## Final Deliverables

A completed incident should produce:

- Incident Report
- Timeline
- Evidence Archive
- Lessons Learned
- Updated Playbooks
- Improved Detection Rules
- Updated Documentation

---

# Incident Response Lifecycle

```text
              PREPARATION
                    │
                    ▼
        DETECTION & ANALYSIS
                    │
                    ▼
 CONTAINMENT → ERADICATION → RECOVERY
                    │
                    ▼
        POST-INCIDENT ACTIVITY
                    │
                    ▼
      Improve preparation for future incidents
```

---

# Quick Revision

| Principle | Why it matters |
|------------|----------------|
| Preparation determines response quality | Good preparation leads to faster and more effective incident response. |
| Verify before acting | Not every alert is a security incident. |
| Prioritize by business impact | Critical systems and high-impact incidents should come first. |
| Identify the root cause first | Otherwise attackers may regain access. |
| Preserve evidence before cleanup | Evidence is essential for investigation and legal purposes. |
| Recovery is not the end | Every incident should strengthen future defenses. |
| Incident Response is a continuous cycle | Lessons learned continuously improve the organization's security posture. |