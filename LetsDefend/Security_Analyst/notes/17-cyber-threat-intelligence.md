# Cyber Threat Intelligence (CTI) Summary

## What is Cyber Threat Intelligence (CTI)?

Cyber Threat Intelligence (CTI) is the process of collecting, processing, analyzing, and interpreting security data from multiple sources to produce **actionable intelligence** that helps organizations prevent, detect, and respond to cyber threats.

### Primary Goals
- Understand attacker **Tactics, Techniques, and Procedures (TTPs)**.
- Collect Indicators of Compromise (IOCs) such as:
  - IP addresses
  - Domains
  - URLs
  - File hashes
  - C2 servers
- Convert raw data into actionable intelligence.
- Reduce the impact of cyber attacks.
- Improve detection and incident response.

---

# CTI Lifecycle

## 1. Planning and Direction

Defines what intelligence is needed and who will consume it.

### Key Questions

- Does the organization have a SOC team?
  - Yes → technical intelligence
  - No → executive summaries

- Has the organization been attacked before?
  - Determines intelligence frequency
  - Prioritizes continuous monitoring

- Are attacks targeting:
  - Organization?
    - Focus on **External Attack Surface Management (EASM)**
  - Individuals?
    - Focus on **Digital Risk Protection (DRP)**

- Are similar organizations being attacked?
  - Enables industry-specific threat intelligence
  - Helps defend against similar campaigns

---

## 2. Information Gathering

Threat intelligence is collected from internal and external sources.

### Common Sources

### External Sources

- Hacker forums
- Ransomware blogs
- Deep/Dark Web
- Telegram
- Discord
- IRC
- ICQ
- Twitter
- LinkedIn
- Facebook
- Cybersecurity blogs
- Research reports
- Public malware sandboxes
- GitHub/GitLab/Bitbucket
- Public cloud buckets
- Shodan
- BinaryEdge
- ZoomEye
- AlienVault OTX
- MalwareBazaar
- Abuse.ch
- VirusTotal
- Hybrid Analysis
- Spamhaus
- URLScan
- Phishunt

### Internal Sources

- Honeypots
- SIEM
- Firewalls
- IDS/IPS
- Public leak databases
- Security logs

---

## 3. Processing

Raw intelligence is cleaned before analysis.

Includes:

- Remove false positives
- Normalize data
- Correlate related events
- Apply rules
- Filter duplicates
- Whitelist legitimate indicators

Output:
- Clean intelligence ready for analysis

---

## 4. Analysis and Production

Analysts convert processed data into actionable intelligence.

Outputs include:

- IOC reports
- Threat actor profiles
- Campaign analysis
- Executive reports
- SOC detection rules

---

## 5. Dissemination and Feedback

Intelligence is shared with appropriate stakeholders.

Examples:

SOC Teams
- IOC feeds
- Detection rules

Managers
- Executive reports
- Threat trends
- Business risks

Feedback improves future intelligence quality by reducing false positives.

---

# Types of Cyber Threat Intelligence

## 1. Technical CTI

Audience:
- SOC Analysts
- Incident Responders

Focus:
- IOCs

Examples

- Malicious IPs
- Domains
- URLs
- File hashes
- C2 servers
- Detection rules

Purpose:
- Direct detection and blocking

---

## 2. Tactical CTI

Audience:
- SOC Managers
- Security Managers

Focus:
- Attacker TTPs

Answers questions like:

- Which vulnerabilities are exploited?
- Which countries are targeted?
- What are attacker motivations?
- What attack methods are used?

Purpose:
- Defensive planning

---

## 3. Operational CTI

Audience:
- Threat Hunters
- Security Managers

Focus:

- Specific campaigns
- Individual threat actors
- Threat hunting

Compared to Tactical CTI:

- Narrower scope
- More investigation-focused
- Supports proactive hunting

---

## 4. Strategic CTI

Audience:
- Executives
- CISOs

Focus:

- Long-term planning
- Budgeting
- Purchasing
- Business risk

Purpose:
- Strategic decision-making

---

# Extended Threat Intelligence (XTI)

Traditional CTI is expanded into:

**XTI = CTI + EASM + DRP**

Purpose:
- Build organization-specific intelligence by mapping threats to the organization's attack surface.

---

# Determining the Attack Surface

An organization's attack surface includes every internet-facing asset.

## Assets to Inventory

- Domains
- Subdomains
- Websites
- Login pages
- Technologies
- CMS
- IP addresses
- IP ranges
- DNS records
- SSL certificates
- Email addresses
- Network services
- Operating systems
- Swift codes
- BIN numbers

---

# Discovering Domains

Methods

## Host.io

Finds:

- Redirect domains
- Co-hosted domains
- Backlinks
- Linked domains

---

## Reverse WHOIS

Tools

- ViewDNS
- Whoxy

Search by:

- Organization name
- Registrant email

Purpose:
Discover all domains registered by the organization.

---

## DNS Analysis

Reverse:

- Name servers
- Mail servers

Tools

- dig
- DNSlytics

---

# Discovering Subdomains

Popular tools

- SecurityTrails
- Sublist3r
- Aquatone
- Assetfinder

Best practice:
Collect data from multiple sources.

---

# Discovering Websites

Use:

- httpx
- httprobe

Purpose:
Identify active HTTP/HTTPS services.

---

# Detecting Login Pages

Indicators

- Login text
- HTML forms
- Username fields
- Password fields
- Login buttons

Usually automated using:

- Python
- Requests
- BeautifulSoup

---

# Identifying Technologies

Purpose

Identify vulnerable software versions.

Tools

- Wappalyzer
- WhatRuns
- BuiltWith
- WhatCMS

Manual methods

- Source code
- HTTP headers
- JavaScript libraries

---

# IP Addresses

Identify via:

- DNS A records
- Domain resolution
- Active scanning

Purpose:

- Vulnerability monitoring
- Port monitoring

---

# IP Blocks

Methods

- WHOIS
- Shodan (org:)
- BinaryEdge
- ZoomEye
- BGP.he.net

Purpose

Identify all owned IP ranges.

---

# DNS Records

Monitor:

- A
- MX
- TXT
- NS
- SPF
- DMARC

Tools

- dig
- DNSlytics

---

# Executive Email Discovery

Useful for protecting executives.

Tools

- RocketReach
- Apollo
- ContactOut
- SalesQL

---

# Network Services and Operating Systems

Methods

Passive

- Shodan
- BinaryEdge

Active

- Port scanning
- Banner grabbing

Purpose

Track:

- Running services
- OS versions
- Vulnerabilities

---

# BIN Numbers & SWIFT Codes

Useful for banking organizations.

BIN tools

- Bincheck
- Bintable
- FreeBinChecker

SWIFT tools

- Wise
- Bank.codes
- TheSwiftCodes

---

# SSL Certificates

Purpose

Track:

- Expired certificates
- Revoked certificates
- Unknown certificates

Tools

- Censys
- crt.sh

---

# Gathering Threat Intelligence

## Internet Search Engines

- Shodan
- BinaryEdge
- ZoomEye
- Censys

Used to identify:

- Open ports
- Services
- Exposed devices

---

## IOC Sources

Popular feeds

- AlienVault OTX
- MalwareBazaar
- Abuse.ch
- VirusTotal
- Hybrid Analysis
- Spamhaus
- URLScan
- Phishunt
- Malware hashes
- Malshare

Collect

- IPs
- Domains
- URLs
- Hashes
- C2 servers

---

## Hacker Forums

Monitor for:

- Attack planning
- Data sales
- Initial access sales
- Threat actor discussions

---

## Ransomware Blogs

Monitor:

- Victim disclosures
- Group announcements
- Target industries

Examples

- LockBit
- Conti
- REvil
- Hive
- Babuk

---

## Black Markets

Monitor for:

- Credit cards
- RDP access
- Logs
- Credentials

---

## Messaging Platforms

Monitor:

- Telegram
- Discord
- IRC
- ICQ

Purpose

Detect:

- Threat actor discussions
- Planned attacks
- Data leaks

---

## Code Repositories

Monitor

- GitHub
- GitLab
- Bitbucket

Search for

- Passwords
- API keys
- Secrets
- Credentials

---

## File Sharing Sites

Examples

- MediaFire
- WeTransfer
- File.io
- UploadFiles

Purpose

Detect leaked organizational documents.

---

## Public Cloud Buckets

Monitor:

- Amazon S3
- Azure Blob
- Google Cloud Storage

Purpose

Detect exposed storage.

---

## Honeypots

Purpose

Collect attacker activity.

Examples

- Cowrie
- Glastopf
- ElasticHoney
- HoneyMail
- Nodepot

---

## Internal Security Products

Excellent intelligence sources:

- SIEM
- IDS
- IPS
- Firewalls

Collect

- Blocked IPs
- Malware hashes
- Detection logs

---

# Threat Intelligence Interpretation

Important steps

- Remove false positives
- Whitelist legitimate assets
- Label data
- Categorize indicators
- Correlate intelligence with the organization's attack surface

Goal:
Produce accurate, actionable intelligence.

---

# Using Threat Intelligence

Threat intelligence supports three major areas:

## 1. External Attack Surface Management (EASM)

Continuously monitors internet-facing assets.

Typical Alerts

- New asset detected
- WHOIS change
- DNS change
- DNS zone transfer
- Internal IP exposed
- Critical open port
- SMTP open relay
- Missing SPF/DMARC
- SSL expired/revoked
- Suspicious redirect
- Subdomain takeover
- Website status change
- Vulnerability detected

---

## 2. Digital Risk Protection (DRP)

Protects organizational reputation and digital assets.

Typical Alerts

- Potential phishing domain
- Rogue mobile app
- Poor IP reputation
- Fake social media account
- Botnet activity
- Dark web mentions
- Threat actor discussions
- Stolen credit cards
- Credential leaks
- Data leaks
- Malware mentioning organization

---

## 3. Cyber Threat Intelligence (CTI)

Provides awareness of:

- Threat campaigns
- Ransomware activity
- Emerging malware
- New attacker infrastructure
- Global cyber threats

Feeds SIEM, SOAR, EDR, and firewalls.

---

# Threat Intelligence Integration with SOC

Threat intelligence becomes most valuable when integrated with security tools.

## SIEM

- IOC enrichment
- Reduce false positives
- Improve correlation

---

## SOAR

- Automated playbooks
- Faster incident response
- IOC enrichment

---

## EDR

- Detect malicious endpoints
- Improve endpoint visibility
- Detect malicious processes and network activity

---

## Firewalls

- Block malicious IPs
- Block malicious domains
- Automatically update blocklists

---

# Key Takeaways

- CTI transforms raw security data into actionable intelligence.
- The CTI lifecycle consists of **Planning → Collection → Processing → Analysis → Dissemination**.
- CTI has four intelligence levels:
  - Technical
  - Tactical
  - Operational
  - Strategic
- XTI combines **CTI + EASM + DRP** for organization-specific intelligence.
- Maintaining a complete attack surface inventory is essential.
- Intelligence should come from multiple internal and external sources.
- Always remove false positives and whitelist trusted indicators before using intelligence.
- Integrating CTI with SIEM, SOAR, EDR, and firewalls significantly improves detection, automation, and response.