# Mitre Att&ck Framework
- The framework is just like a model of the steps an attacker takes during an attack
- MITRE ATT&CK that stands for Adversarial Tactics, Techniques, and Common Knowledge
- Introduced by MITRE in 2013
- MITRE, a US-based organization that produces innovative solutions to advance national security was founded in 1958
- SOC Analysts can clearly see the actions that should be taken for each stage of the cyber attack and that it as a reference. 
- Attack detection and mitigation techniques developed against cyber attacks can be used more effectively, cyber attacks can be mapped, an in-depth report can be written and the details of the attack can be archived for a later use.

---

## MITRE ATT&CK Matrix
A visualization tool used to classify and see attack methods of cyber attackers.

**Types:** 
- Enterprise Matrix: <https://attack.mitre.org/matrices/enterprise/>
  - Enterprise matrix is the first matrix created by MITRE
  - A lot more information in this matrix than other matrices
  - Mainly used to understand the cyber attacks on large organizations.
  
  | Matrix | Sub-matrices |
|--------|--------------|
| Enterprise Matrix | PRE, Windows, macOS, Linux, Cloud, Network, Containers |

- Mobile Matrix: <https://attack.mitre.org/matrices/mobile/>
  - Contains information about the cyber security of mobile devices
  - Comparing the Enterprise Matrix, it contains less information
  
  | Matrix | Sub-matrices |
|--------|--------------|
| Mobile Matrix | Android, iOS |

- ICS (Industrial Control Systems) Matrix: <https://attack.mitre.org/matrices/ics/>
  - Contains the information collected for the cyber security of devices in the industrial control systems
  - Used to provide cyber security and analyses of an ICS
 
---

## Tactics
- Expresses the purpose of the cyber attacker and the reason for his action
- Used to group cyber attacker behaviors and see the attack steps

**Types:**
| Enterprise Matrix | Mobile Matrix | ICS Matrix |
|-------------------|---------------|------------|
| Reconnaissance | Initial Access | Initial Access |
| Resource Development | Execution | Execution |
| Initial Access | Persistence | Persistence |
| Execution | Privilege Escalation | Privilege Escalation |
| Persistence | Defense Evasion | Evasion |
| Privilege Escalation | Credential Access | Discovery |
| Defense Evasion | Discovery | Lateral Movement |
| Credential Access | Lateral Movement | Collection |
| Discovery | Collection | Command and Control |
| Lateral Movement | Command and Control | Inhibit Response Function |
| Collection | Exfiltration | Impair Process Control |
| Command and Control | Impact | Impact |
| Exfiltration | Network Effects | |
| Impact | Remote Service Effects | |
| <https://attack.mitre.org/tactics/enterprise/>| <https://attack.mitre.org/tactics/mobile/>| <https://attack.mitre.org/tactics/ics/>|

---

## Techniques and Sub-Techniques
- Shows the methods used by the attacker to achieve his goal and how he conducted the attack exactly
- A **Technique** describes **how** an adversary achieves a tactical objective.
- A **Sub-technique** provides a more specific implementation of a parent technique.
  - Example:
    - **Technique:** OS Credential Dumping (T1003)
    - **Sub-technique:** LSASS Memory (T1003.001)
- MITRE ATT&CK techniques are grouped into three matrices:


  | Matrix | Techniques | Sub-techniques |
|---------|-----------:|---------------:|
| Enterprise | 222 | 475 |
| Mobile | 77 | 47 |
| ICS | 79 | 18 |

> **Note:** These counts may be updated as time goes on and hence the need to refer to the MITRE ATT&CK website for the latest information.

### Official MITRE ATT&CK Resources

| Matrix | Reference |
|---------|-----------|
| Enterprise | https://attack.mitre.org/techniques/enterprise/ |
| Mobile | https://attack.mitre.org/techniques/mobile/ |
| ICS | https://attack.mitre.org/techniques/ics/ |

---


## What is a Procedure?

A **Procedure** is a real-world example of **how a threat actor or malware implements a technique or sub-technique**.

It answers questions such as:

- Which tool was used?
- Which malware family performed the action?
- How was the technique executed in practice?

For example, a malware family may use the **OS Credential Dumping** technique by dumping credentials from the **LSASS** process.


---

## MITRE ATT&CK Mitigations
- **Mitigations** are security measures and defensive actions that organizations can implement to reduce the impact or likelihood of adversary techniques described in the MITRE ATT&CK Framework.

Each mitigation contains:

- **Mitigation ID**
- **Mitigation Name**
- **Description**
- **Recommended defensive actions**

Mitigations help defenders understand how to prevent, reduce, or respond to attacker behaviors.

---

**Types:**


| Matrix | Description |
|--------|-------------|
| Enterprise Mitigations | Defensive measures for enterprise environments |
| Mobile Mitigations | Defensive measures for mobile platforms |
| ICS Mitigations | Defensive measures for industrial control systems |


## Mitigation Overview

| Matrix | Number of Mitigations |
|--------|----------------------:|
| Enterprise | 44 |
| Mobile | 13 |
| ICS | 52 |


## MITRE ATT&CK Mitigation Resources

| Matrix | Reference |
|--------|-----------|
| Enterprise Mitigations | https://attack.mitre.org/mitigations/enterprise/ |
| Mobile Mitigations | https://attack.mitre.org/mitigations/mobile/ |
| ICS Mitigations | https://attack.mitre.org/mitigations/ics/ |

---


## Groups
- Advanced Persistent Threat (APT) Groups, are the hacker groups that may include many 
different people and groups that carry out cyber attacks in a targeted and systematic way, 
with governments support from time to time.

---

## MITRE ATT&CK Software
- The tools, malware, scripts, and applications used by adversaries to carry out attacks.

Each software entry includes:

- **Software ID**
- **Software Name**
- **Description**
- **Associated ATT&CK Techniques**
- **Threat Groups (APT groups) known to use the software**

---

## Software Information

| Attribute | Description |
|-----------|-------------|
| Software ID | Unique identifier assigned by MITRE |
| Name | Name of the malware or tool |
| Description | Overview of the software's functionality |
| Techniques | ATT&CK techniques associated with the software |
| Groups | Threat actors or APT groups known to use the software |


## Software Statistics

| Category | Count |
|----------|------:|
| Software | 718 |

> **Note:** This count was accurate at the time the training was created and may change as MITRE ATT&CK is regularly updated.


## MITRE ATT&CK Software Resource

| Resource | Link |
|----------|------|
| MITRE ATT&CK Software | https://attack.mitre.org/software/ |


The Software section helps defenders:

- Identify malware and offensive tools used by threat actors.
- Understand which ATT&CK techniques a tool implements.
- Associate malware with known APT groups.
- Improve threat hunting and detection engineering by mapping observed tools to ATT&CK.

---






















