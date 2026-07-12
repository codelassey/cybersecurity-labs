# Cyber Kill Chain
- Cyber Kill Chain framework created by Lockheed Martin in 2011
- Lockheed Martin is a security and aerospace coorperation established in 1995
- Used to model attacker behaviour
- Important for a SOC analyst understand the stages of a cyber attack and to determine which action
the cyber attack starts with and which action(s) follows.

## Steps of the Kill Chain
There are several actions that an attacker can take before, 
during, and after a successful cyber attack. These actions are 
sequential, and if the attacker fails at one stage, it is not 
possible to execute the next step of the cyber attack.

### 1. Reconnaissance
Obtaining info about the target actively or passively.
- Active: Probing eg submitting a request to a web server, port scanning, etc
- Passive: (without physical contact) eg searching for info via the wayback machine

At the Recon stage, the following may be performed by the adversary and the defender
| Adversary | Defender |
|------------|-----------|
| Gather version information about software and servers | Detect information disclosure through vulnerability assessments and penetration testing |
| Collect employee email addresses | Monitor for exposed information using threat intelligence and data leak monitoring |
| Use social media to gather employee information | Minimize publicly available employee and organizational information (OSINT reduction) |
| Identify internet-facing devices | Monitor network traffic and exposed services using firewalls, IDS/IPS, and asset management |
| Identify vulnerabilities on internet-facing servers | Regularly patch and update systems to reduce exploitable vulnerabilities |
| Identify the organization's public IP address ranges | Monitor external attack surface and secure publicly exposed assets |

For my consuumption..
```
APT Group: Lazarus Group-APT38
Year of Cyber Attack Detection: 2019
Target: Institution in theh financial sector
CVE: CVE-2019-0604
Associated with: Microsoft SharePoint product
After the successful exploit: **Powershell Empire** backdoor was deployed to the target system
```

### 2. Weaponization
This is step 2 of the cyber kill chain. Adversary uses info gathered from recon to access or script tools
needed for the attack. eg preparing a malicious email if phishing is to be carried out.

At the weaponization stage, the adversary may employ alternative attack techniques. For the 
defenders, they can't actually halt the attacker's prep but can put some measures in place..
| Adversary | Defender |
|------------|-----------|
| Create malware to compromise target systems | Regularly assess systems for known security vulnerabilities |
| Develop exploits targeting software or system weaknesses | Promptly apply security patches and software updates |
| Create malicious phishing content (e.g., email templates and weaponized documents) | Monitor emerging attack techniques and update email security controls and detection rules accordingly |
| Select the most effective attack tool or technique for the target environment | Track newly discovered malware, exploits, and attacker tools to understand their impact and improve detection capabilities |


### 3. Delivery
At this stage, the attacker executes the cyber attack that he prepared for in the preceding phases.
This is how I like to call it: the weapon is now delivered to the target.

The adversary can chose any vector of attack and so can defenders also set precautions that can reduce
the risk associated with the attack..

| Adversary | Defender |
|------------|-----------|
| Deliver a malicious URL via email | Treat email links with caution and analyze suspicious URLs in a sandbox before accessing them |
| Deliver malware as an email attachment | Scan email attachments with antivirus or sandbox solutions before opening them |
| Deliver malware through a compromised or malicious website | Deploy secure web gateways, web filtering, and endpoint protection to detect and block malicious downloads |
| Deliver malicious URLs through social media | Train users to recognize social engineering attempts and avoid suspicious links on social platforms |
| Deliver malware through social media | Implement user awareness training and endpoint security controls to detect and prevent malware execution |
| Upload malware directly to a target server (when direct access is available) | Continuously monitor server access, log authentication events, and investigate unauthorized activities |
| Install malware via removable media (eg USB devices) | Restrict or disable unauthorized USB devices and monitor endpoint activity for suspicious behavior |
| Attempt delivery through any attack vector | Use layered security controls such as firewalls, email security gateways, EDR, and IDS/IPS to detect and block malicious activity |
| Deliver malicious content while evading detection | Analyze suspicious events, investigate anomalies, and identify the initial point of compromise as early as possible |


### 4. Exploitation
The fourth step! The attacker ensures that the malicious content provided to the victim in the previous step gets activated in this stage.
Basically, I call this the test of fate lol. The attacker's weapon mustt either work or the attack fails..

| Adversary | Defender |
|------------|-----------|
| Execute an exploit targeting a hardware vulnerability | Keep hardware firmware up to date and monitor systems for signs of exploitation or abnormal behavior |
| Execute an exploit targeting software or operating system vulnerabilities | Track newly disclosed vulnerabilities, apply security patches promptly, and implement detection rules for known exploits |
| Execute malware on the target system | Monitor endpoint activity using EDR/XDR solutions to detect and respond to malicious execution |
| Attempt to exploit previously unknown (zero-day) vulnerabilities | Continuously monitor systems for anomalies and suspicious behaviors that may indicate exploitation |
| Exploit user actions to gain code execution | Train employees to recognize suspicious files, links, and unexpected requests before interacting with them |
| Exploit insecure internally developed applications | Provide secure coding training and perform regular security testing of custom applications |
| Exploit weaknesses in organizational assets | Conduct regular penetration tests and automated vulnerability assessments to identify and remediate weaknesses |
| Abuse excessive privileges after successful exploitation | Enforce the Principle of Least Privilege (PoLP) by granting users and services only the permissions they require |

### 5. Installation
This is the fifth step. So like I said earlier on, if the exploit fails, game over. But if
it succeeds, then the attacker will now seek to maintain persistence on the target system.

| Adversary | Defender |
|------------|-----------|
| Install malware on the victim's device | Use EDR/XDR solutions to detect malware installation and monitor endpoint configuration changes |
| Install a backdoor to maintain unauthorized access | Perform continuous threat hunting and monitor systems for persistence mechanisms and unauthorized access |
| Install a web shell on a compromised web server | Monitor web servers for unauthorized file creation, web shell indicators, and suspicious web requests |
| Create services, scheduled tasks, or firewall rules to establish persistence | Monitor for unauthorized changes to services, scheduled tasks, startup items, registry keys, and firewall configurations |
| Modify system configurations while avoiding detection | Restrict access to critical files and directories, and continuously monitor for unauthorized modifications |
| Execute malicious processes on the compromised system | Detect suspicious process creation and behavior through process monitoring and EDR telemetry |
| Abuse administrative privileges to maintain persistence | Enforce the Principle of Least Privilege (PoLP) and grant administrative privileges only when necessary |
| Execute unsigned or untrusted binaries | Implement application allowlisting and permit only trusted, digitally signed executables to run |
| Hide malicious activity to remain undetected | Continuously monitor endpoint and network activity, investigate anomalies, and identify the root cause of suspicious events |


### 6. Command and Control
This is the 6th step of thhe attaack. The attaker attempts to send remote commands to the
target and execute them using a C2 server

| Adversary | Defender |
|------------|-----------|
| Configure a Command and Control (C2) server to communicate with compromised systems | Identify known C2 frameworks and tools present on endpoints through threat hunting and endpoint monitoring |
| Establish communication between the victim and the C2 server | Monitor network traffic for suspicious outbound connections that may indicate C2 communication |
| Configure the compromised device to maintain communication with the C2 infrastructure | Block known malicious C2 IP addresses, domains, and indicators using Cyber Threat Intelligence (CTI) feeds and security controls such as firewalls, IDS/IPS, and DNS filtering |
| Use covert or encrypted channels to evade detection | Analyze network traffic for anomalous communication patterns, beaconing behavior, and unusual encrypted outbound connections |
| Maintain reliable communication with the compromised host | Continuously perform Network Security Monitoring (NSM) to detect, investigate, and respond to potential C2 traffic |


### 7. Actions on Objectives
"Actions on Objectives" is the seventh and final step of the Cyber Kill Chain. This confirms
the attacker got whatt they came for.
At this point, the attacker takes the actions planned at the first stages of the cyber attack.

| Adversary | Defender |
|------------|-----------|
| Encrypt files using ransomware | Detect abnormal file encryption activity and isolate affected systems to contain the attack |
| Exfiltrate sensitive data from the environment | Deploy Data Loss Prevention (DLP) solutions and monitor outbound network traffic for unauthorized data transfers |
| Delete or destroy critical system data | Restrict access to critical files, maintain secure backups, and monitor file deletion or modification activities |
| Escalate privileges and move laterally across the network | Enforce the Principle of Least Privilege (PoLP), monitor privileged account activity, and detect lateral movement attempts |
| Harvest user credentials to access additional systems | Detect credential theft attempts, monitor authentication logs, and enforce Multi-Factor Authentication (MFA) where possible |
| Collect sensitive information from compromised systems | Restrict access to sensitive files and databases, and continuously monitor access to critical resources |
| Modify, manipulate, or tamper with data | Monitor file integrity, detect unauthorized changes, and investigate anomalies affecting critical data |
| Perform malicious actions aligned with the attack objective | Continuously monitor systems and network traffic, investigate anomalies, and respond promptly to malicious activity |
| Transfer stolen data outside the organization | Restrict outbound network access, inspect external communications, and block unauthorized data exfiltration attempts |
| Access critical systems or databases without authorization | Monitor user activity, detect unauthorized access attempts, and regularly review permissions on sensitive systems |






























