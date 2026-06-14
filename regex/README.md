# Regular Expressions for Cybersecurity (Regex)
> Within this folder, I compile some regex resources and commands useful to every Cybersecurity Analyst for easy reference. 
> I will also include a jupter notebook (`regex-cybersecurity-labs.ipynb`) containing using Regex commands explained here to solve different kind of scenarios.. or I guess you can just call them problems.
---
 
## Table of Contents

1. [What Is a Regular Expression?](#1-what-is-a-regular-expression)
2. [Why Regex Matters in Cybersecurity](#2-why-regex-matters-in-cybersecurity)
3. 



## 1. What Is a Regular Expression?
 
A **regular expression** (regex) is a sequence of characters that defines a search pattern. Think of it like `Ctrl+F` on your keyboard that doesn't just find exact strings but finds patterns of strings. 
Instead of searching for the literal text `"192.168.1.1"`, you can write a pattern that matches *any* IP address formatted as four groups of digits separated by dots.
 
A regex engine reads your pattern and builds a finite automaton that scans text character by character, transitioning between states until it either confirms a match or rejects the substring.
 
```regex
Variable:       "Failed login from 10.0.0.5 at 2024-01-15"

Pattern:        \d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}
Or Pattern      \d+\.\d+\.\d+\.\d+
Match found:    "10.0.0.5"
```
 
Regex is language-agnostic.. thus the same pattern concepts apply in Python, bash (`grep`, `sed`, `awk`), Splunk SPL, Suricata rules, YARA signatures, JavaScript, Go, and virtually every other tool a SOC analyst touches daily.

---

## 2. Why Regex Matters in Cybersecurity
 
Security analysts deal with *massive* amounts of unstructured text (auth logs, firewall logs, PCAP metadata, EDR telemetry, threat intel feeds, SIEM dashboards) and doing this work manually is both slow and error-prone. Regex automates the extraction and classification of indicators.
 
Here's where regex shows up in the SOC:
 
| Use Case | What Regex Does |
|---|---|
| **Log parsing** | Extracts usernames, IPs, timestamps, error codes from raw log lines |
| **IOC extraction** | Pulls hashes, IPs, URLs, email addresses from threat reports |
| **Alert tuning** | Matches patterns in SIEM rules to reduce false positives |
| **IDS/IPS signatures** | Suricata and Snort use PCRE (Perl Compatible Regular Expressions) in rule content matching |
| **YARA rules** | Malware signatures use regex-like string patterns |
| **SOAR playbooks** | N8N, Shuffle, and similar tools use regex in conditional logic to triage alerts |
| **DLP (Data Loss Prevention)** | Detect credit cards, SSNs, API keys in outbound traffic |
| **Incident Response** | Rapidly grep through filesystem artifacts, memory dumps, event logs |
| **Threat hunting** | Search SIEM indexes for anomalous patterns that didn't trigger any alert |
 
---

##
