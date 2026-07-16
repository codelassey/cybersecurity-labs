# Investigating SIEM Alerts

## Table of Contents
- [Overview](#overview)
- [Alert Detection and Monitoring](#alert-detection-and-monitoring)
- [Alert Ownership and Case Creation](#alert-ownership-and-case-creation)
- [Playbook-Driven Investigation](#playbook-driven-investigation)
- [Network and Log Analysis](#network-and-log-analysis)
- [Threat Intelligence Validation](#threat-intelligence-validation)
- [Endpoint Analysis](#endpoint-analysis)
- [Containment](#containment)
- [Closing the Alert](#closing-the-alert)
- [Key Takeaways](#key-takeaways)

---

## Overview

> **Note:** These notes are based on a phishing SIEM alert that I successfully investigated in the LetsDefend SOC environment. (SOC282 specifically)

A SIEM (Security Information and Event Management) platform collects, correlates, and analyzes security events to generate alerts when suspicious activity matches predefined detection rules.

During the investigation, the objective was to determine:

- Whether the alert was legitimate.
- Whether compromise occurred.
- The overall impact of the incident.
- Appropriate containment and response actions.

---

## Alert Detection and Monitoring

The investigation began from the **Monitoring** page, where alerts are organized into:

- Main Channel (new alerts)
- Investigation Channel (assigned alerts)
- Closed Alerts (completed investigations)

Each alert provides important information such as:

- Severity
- Date
- Rule Name
- Event ID
- Alert Type

Alerts can also be filtered by severity, role, or alert category to prioritize investigations.

---

## Alert Ownership and Case Creation

Before investigating, ownership of the alert must be assigned.

After taking ownership:

- The alert moves to the Investigation Channel.
- A case is created.
- Case Management opens automatically.
- A playbook is assigned to guide the investigation.

Creating a case ensures that every investigation is properly documented and tracked.

---

## Playbook-Driven Investigation

Each alert type has its own investigation workflow known as a **playbook**.

The playbook provides standardized investigation steps to ensure:

- Consistent analysis
- Complete evidence collection
- Proper documentation
- Reduced investigation errors

For this phishing investigation, the playbook guided every stage of the incident response process.

---

## Network and Log Analysis

Log analysis was performed to determine whether the malicious infrastructure had been contacted.

The investigation included:

- Identifying the affected endpoint IP address
- Searching logs for network activity
- Reviewing source and destination IP addresses
- Checking raw log data
- Looking for communication with Command-and-Control (C2) servers

This stage helps determine whether malicious URLs or payloads were actually accessed.

---

## Threat Intelligence Validation

Indicators of Compromise (IOCs) identified during the investigation were validated using the Threat Intel module.

Examples of IOCs include:

- Malicious IP addresses
- Domains
- URLs
- File hashes

Threat intelligence enrichment helps confirm whether observed indicators are associated with known malicious activity.

---

## Endpoint Analysis

Endpoint Security was used to determine whether malware executed on the affected host.

The investigation focused on:

### Host Information

- Hostname
- IP address
- Operating System
- User information

### Process Analysis

- Running processes
- Suspicious parent-child relationships
- Process hashes
- Network activity initiated by processes

### Network Activity

Reviewed outbound and inbound connections for signs of:

- Command-and-Control communication
- Suspicious external connections

### Terminal History

Reviewed executed commands to identify:

- Suspicious PowerShell activity
- Command execution
- Administrative abuse

### Browser History

Examined browser activity for:

- Malicious websites
- Download links
- Phishing pages

Endpoint analysis determines whether the threat remained limited to email delivery or progressed into endpoint compromise.

---

## Containment

If malicious activity is confirmed, containment should be performed immediately to prevent further damage.

Containment objectives include:

- Preventing data loss
- Blocking unauthorized access
- Stopping lateral movement
- Preventing further malware execution

Modern EDR solutions can isolate compromised endpoints while investigations continue.

---

## Closing the Alert

After completing the investigation:

- Determine whether the alert is a **True Positive** or **False Positive**.
- Record all findings in the Analyst Notes.
- Document supporting evidence.
- Close the investigation.

Completed investigations can later be reviewed from the Closed Alerts section together with the official incident report.

---

## Key Takeaways

- SIEM alerts should follow a structured investigation process.
- Taking ownership creates accountability and begins formal case management.
- Playbooks provide consistent investigation procedures.
- Log analysis confirms malicious network activity.
- Threat intelligence validates indicators of compromise.
- Endpoint analysis determines whether malware executed.
- Containment reduces the impact of confirmed incidents.
- Proper documentation is essential for future investigations and knowledge sharing.