# SOC Fundamentals - Notes

## Table of Contents

1. [Course Overview](#1-course-overview)
2. [SOC Types and Roles](#2-soc-types-and-roles)
   - What is a SOC
   - SOC models
   - People, Process, Technology
   - SOC roles
3. [SOC Analyst and Their Responsibilities](#3-soc-analyst-and-their-responsibilities)
4. [SIEM and Analyst Relationship](#4-siem-and-analyst-relationship)
   - Question
5. [Log Management](#5-log-management)
   - Questions
6. [EDR - Endpoint Detection and Response](#6-edr--endpoint-detection-and-response)
   - Questions
7. [SOAR (Security Orchestration, Automation and Response)](#7-soar-security-orchestration-automation-and-response)
8. [Threat Intelligence Feed](#8-threat-intelligence-feed)
   - Question
9. [Common Mistakes Made by SOC Analysts](#9-common-mistakes-made-by-soc-analysts)
10. [Quiz](#10-quiz)

---

## 1. Course Overview

- Covers: SOC structure, SOC operation, SOC tools/products, how analysts should use those tools, and common analyst mistakes.
- Aimed at both aspiring SOC analysts (focus on SIEM, Log Management, EDR) and current analysts looking to sharpen best practices (via "Quick Tips" sections and real mistake examples).

---

## 2. SOC Types and Roles

### What is a SOC?
A **Security Operations Center (SOC)** is where the information security team continuously monitors and analyzes an organization's security - detecting, analyzing, and responding to incidents using people, process, and technology.

### SOC Models
- **In-house SOC** - built and run entirely internally; requires sustained budget.
- **Virtual SOC** - no permanent facility; team often works remotely.
- **Co-Managed SOC** - internal staff paired with an external Managed Security Service Provider (MSSP); coordination is key.
- **Command SOC** - oversees multiple smaller SOCs across a large region (common with large telecoms, defense agencies).

### People, Process, Technology
- **People** - need trained staff who can adapt to constantly evolving attack techniques and are willing to research new threats.
- **Process** - must align with standards like NIST, PCI, HIPAA; requires standardized actions so nothing is missed.
- **Technology** - tools for pen-testing, detection, prevention, and analysis; the "best" product on the market isn't always the best fit for your org (budget and other factors matter).

### SOC Roles
- **SOC Analyst** (Level 1/2/3) - classifies alerts, investigates root cause, advises remediation.
- **Incident Responder** - performs initial assessment of security breaches; responsible for threat detection.
- **Threat Hunter** - proactively searches for threats/vulnerabilities using manual + automated techniques, aiming to catch APTs and sophisticated attacks before they cause damage; deep knowledge of the org's infrastructure and current threat landscape.
- **Security Engineer** - maintains SIEM/SOC product infrastructure (e.g., connecting SIEM with SOAR).
- **SOC Manager** - handles budgeting, strategy, staffing, and operational coordination (not technical work).

---

## 3. SOC Analyst and Their Responsibilities

- A SOC Analyst is typically the **first person** to investigate a threat, escalating to supervisors when needed.
- **Upside of the role:** attack techniques and malware constantly evolve, so the work stays varied even though the tools used stay largely the same.
- **A typical day:** reviewing SIEM alerts, determining which are real threats, and using tools like EDR, Log Management, and SOAR to reach a conclusion.
- **Core skills needed:**
  - **Operating Systems** - you must know what's "normal" (e.g., typical Windows services) to spot what's abnormal.
  - **Network** - needed to check for devices contacting known-malicious IPs/URLs, and to investigate potential data leaks.
  - **Malware Analysis** - needed to understand a sample's true behavior (malware often disguises itself), especially to identify its C2 address and whether any devices are communicating with it.

---

## 4. SIEM and Analyst Relationship

- **SIEM** (Security Information and Event Management) does real-time event logging across an environment, with the goal of detecting security threats.
- As an analyst, the most relevant SIEM features are **data collection/filtering** and **alerting** on suspicious events.
  - *Example rule:* 20 failed login attempts within 10 seconds is very unlikely to be a genuine mistyped password, so a threshold-based rule triggers an alert for this pattern.
- **Popular SIEM products:** IBM QRadar, ArcSight ESM, FortiSIEM, Splunk.
- **Analyst's role:** SOC analysts mainly **triage alerts** (rule creation/tuning is usually a separate team's job). The core task is determining whether a generated alert is a real threat or a false positive, using details like hostname, IP, and file hash, often cross-referencing other tools (EDR, Log Management, Threat Intel).
- On LetsDefend's Monitoring page: new alerts appear in the shared **Main Channel**; clicking **Take Ownership** moves an alert to your **Investigation Channel** so teammates can see what you're working on and avoid duplicating effort.
- **Quick Tip:** Analysts should recognize and flag **false-positive rule patterns** back to the SIEM team to help tune alerting (e.g., a rule flagging the keyword "union" for SQL injection detection triggering on an ordinary Google search URL containing that word).

### Question

**When you close an alert, which channel (tab on the monitoring page) can you access it from?**
Answer: **Closed Alerts**

---

## 5. Log Management

- **Log Management** centralizes logs from many sources (web, OS, firewall, proxy, EDR, etc.) into one place, saving time and reducing error versus querying each system separately.
- On LetsDefend's Log Management page, sources like Proxy, Exchange, and Firewall all appear under a single "Type" field, queryable together.
- **Primary uses:**
  - Checking whether any device has communicated with a known C2 address (e.g., after identifying malware calling out to `letsdefend.io`, search that domain across all logs).
  - After containing one confirmed-compromised host, searching Log Management for the same suspicious IP to check whether **other** devices were also communicating with it - since the original alert may not have caught everything.

### Questions

**What source IP address entered the URL 'https://github.com/apache/flink/compare'?**
Searched Log Management for: `Raw Log contains "https://github.com/apache/flink/compare"`
Answer: `172.16.17.54`

**What is the type of log that has a destination port number of 52567 and a source IP address of 8.8.8.8?**
Found by filtering on the destination port number.
Answer: `DNS`

---

## 6. EDR - Endpoint Detection and Response

- **EDR** combines continuous real-time endpoint monitoring/data collection with rules-based automated response and analysis.
- **Common EDR products:** CarbonBlack, SentinelOne, FireEye HX.
- On LetsDefend's "Endpoint Security" page: search individual endpoints, or search across **all hosts** using an IOC (IP, file hash, process name, etc.). Each device view shows Browser History, Network Connections, and Process List.
- **Live Investigation** - the **Connect** button lets an analyst access the machine directly to continue investigating.
- **Containment** - isolating a compromised device (via EDR's containment feature) prevents the attacker from reaching the internal network or moving laterally. Isolated devices can still communicate with the EDR center, so analysis can continue even while contained.
- **Quick Tip:** Given any IOC (file hash, filename, etc.), search across **all hosts** in EDR to see if the same indicator appears elsewhere - this helps scope how many devices are affected by an attack.

### Questions

**What is the hostname of the device where the "nmap" file with a hash value of "83e0cfc95de1153d405e839e53d408f5" is executed?**
Filtered raw logs for "nmap," which surfaced relevant events; then queried Endpoint Security for the IP that accessed the site to download/execute nmap.
Answer: `ericprod`

**A "Ps1.hta" file was executed on a device with the hostname "Roberto". What is the complete CMD command?**
Filtered for the hostname and reviewed terminal history to find the full command.
Answer: `C:/Windows/System32/mshta.exe C:/Users/roberto/Desktop/Ps1.hta`

---

## 7. SOAR (Security Orchestration, Automation and Response)

- SOAR lets security tools work together, automating repetitive analyst tasks (e.g., automatically checking VirusTotal for a SIEM alert's source IP).
- **Common SOAR products:** Splunk Phantom, IBM Resilient, Logsign, Demisto.
- **Key benefits:**
  - **Saves time** - automates workflows like IP reputation checks, hash lookups, and sandbox detonation of acquired files.
  - **Centralization** - brings multiple tools (sandbox, log management, third-party tools) into one integrated platform.
  - **Playbooks** - standardized, step-by-step investigation guides for different alert scenarios; ensures the whole team follows the same procedure (e.g., everyone checks IP reputation, not just some analysts).
- On LetsDefend, **Case Management** serves the SOAR role: open/closed case tickets exist on the Monitoring page, and opening a case shows an auto-assigned **playbook** to guide the investigation of the linked alert.

---

## 8. Threat Intelligence Feed

- A **Threat Intelligence Feed** provides third-party data (malware hashes, C2 domains/IPs, etc.) built from artifacts of previously observed malicious activity.
- Used to check whether a hash/IP/etc. on hand has a known history of malicious use.
- **Free/popular sources:** VirusTotal, Talos Intelligence.
- **Important caveats:**
  - A "clean" feed result doesn't guarantee a file is safe - still perform proper static/dynamic analysis rather than assuming innocence.
  - IP addresses can change ownership/purpose over time - an IP flagged as malicious in the past (e.g., a shut-down attacker C2 server) could later be reassigned to something entirely legitimate (e.g., someone's personal blog), so historical flags shouldn't be treated as proof of current malicious content.

### Question

**What is the data source of the "e1def6e8ab4b5bcb650037df234e2973" hash on the threat intel page?**
Queried the Threat Intel dashboard with the hash, which returned metadata including the data source.
Answer: `abusech`

---

## 9. Common Mistakes Made by SOC Analysts

- **Over-reliance on VirusTotal results** - new/AV-bypassing malware may not be flagged; treat VT as a *supporting* tool, not a final verdict.
- **Hasty sandbox analysis** - a 3-4 minute sandbox run may miss malware that detects sandboxing or delays activation by 10-15+ minutes; analysis should run as long as practical, ideally in a real environment.
- **Inadequate log analysis** - e.g., after finding malware on one host calling out to a C2 domain, failing to check Log Management for **other** devices contacting the same address.
- **Overlooking VirusTotal result dates** - a cached "clean" result may be stale; an attacker could repurpose a previously-clean URL for malicious content afterward, so always re-run a fresh search rather than trusting the cache.

---

## 10. Quiz

**Q1. Which type of SOC team does not have its own facility and often works remotely in different locations?**
- In-house SOC
- Command SOC
- **Virtual SOC**
- Co-Managed SOC

**Q2. I am responsible for connecting security products. My job title is ...**
- Threat hunter
- Security analyst
- **Security engineer**
- Penetration tester

**Q3. What is SOC?**
- **Security Operation Center  **
- Security Operation Capability
- Secure Operation Center
- Secure Operation Capability

**Q4. Which tool is the most important for a SOC analyst?**
- SIEM
- Log Management
- EDR
- **All of this, and much more  **

**Q5. Which type of SOC model corresponds to the following definition: "consists of internal SOC personnel working with an external Managed Security Service Provider"?**
- In-house SOC
- **Co-Managed SOC  **
- Command SOC
- Virtual SOC

**Q6. Which SOC position corresponds to the following definition: "A team member whose purpose is to find vulnerabilities before the attacker can exploit them in an attack."?**
- **Threat Hunter  **
- SOC Manager
- Incident Responder
- Security Engineer

**Q7. What is the goal of a SIEM?**
- To store logs for legal reasons
- To be confirmed to ISO 27001
- **To provide the real time logging of events in an environment.  **
- To make readable for everyone

**Q8. Which LetsDefend's page is the SIEM?**
- Log Management
- Endpoint Security
- **Monitoring  **
- Threat Intel

**Q9. What is an EDR?**
- Software not only monitors endpoints, but also emails, servers and the cloud.
- Software that provides network-wide visibility to SOC teams to detect the behavior of potentially hidden attackers targeting physical, virtual and cloud infrastructures.
- Software that combines managed solutions (managed by a cybersecurity vendor) and incident detection and response service.
- **A software that monitors the terminals (computers, servers, tablets, phones...) and not the information system network.  **

**Q10. What are the different steps of the lifecycle for the NIST, of a incident?**
- Reconnaissance, Weaponization, Delivery, Exploitation, Installation, Command and Control, Actions on objectives
- **Preparation, Detection/Analysis, Containment/Eradication and Recovery, Post-Event Activity  **
- Identify potential risks, Analysing the risk, Evaluating the risk, treating the risk, Monitoring & reviewing the risk
- Discover, Assess, Report, Remediate, Verify

**Q11. What can be used to conduct an analysis with SOAR?**
- Information Management System
- Ticket
- **Playbook  **
- A Google Search

**Q12. Which information do you not have in the Threat Intelligence Feed?**
- Data Source
- Score
- Data Type
- **A sample of the infected file  **

**Q13. Which is a common mistake for SOC analysts?**
- **Insufficient log analysis  **
- Putting aside the managerial tasks
- Running scripts with root privileges
- Have a burn-out