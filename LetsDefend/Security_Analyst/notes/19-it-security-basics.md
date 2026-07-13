# IT Security Basics for Corporates 

## Overview
A strong security posture is built on **visibility, prevention, detection, and recovery**. Key areas include:

- Asset Inventory
- Backups
- Phishing Protection
- Secure Browsing
- Patching
- Access Control
- Risk Management
- Network Security
- Incident Response

---

# 1. Asset Inventory

Maintain an up-to-date inventory of:
- Hardware
- Software (including versions)
- Users and assets
- Last audit/update date

### Best Practices
- Remove **End-of-Life (EOL)** hardware/software.
- Enable **Secure Boot** on supported devices.
- Allow only approved software (using GPO, Intune, AppLocker).
- Apply **security hardening** (GPO, Intune, Ansible, CIS Benchmarks).
- Deploy **Antivirus/EDR** across all critical systems.

---

# 2. Backups

Backups are the **last line of defense**, especially against ransomware.

### 3-2-1 Rule
- **3** copies of data
- **2** different storage media
- **1** offsite backup

### 3-2-1-1-0 Rule
Adds:
- **1** offline backup
- **0** restoration errors (test backups regularly)

### Best Practices
- Retain backups for at least **30 days**.
- Test restores regularly (at least annually).
- Ensure backups are isolated from production systems.

---

# 3. Phishing Protection

Implement:
- Email spam filtering
- Attachment scanning
- Email security gateway
- Reporting process for suspicious emails
- Regular phishing awareness training and simulations

---

# 4. Internet Browsing Protection

Reduce web-based threats by:
- DNS filtering
- Blocking malicious/suspicious domains
- Blocking URL shorteners where appropriate
- Centrally managing browser security settings
- Restricting browser extensions/plugins

---

# 5. Patching

Apply security updates as quickly as possible.

### Prioritize patches based on:
- CVSS score
- Internet-facing exposure
- Active exploitation (0-days)
- Business criticality

Enable automatic updates whenever possible.

---

# 6. Access Control

Apply the **Principle of Least Privilege**.

### Best Practices
- Strong password policy
- Multi-Factor Authentication (MFA)
- Remove unused accounts
- Eliminate shared accounts
- Minimize privileged accounts
- Regularly audit user activity
- Adopt Zero Trust principles

---

# 7. Risk Analysis

Perform regular risk assessments to:
- Prioritize security investments
- Identify critical assets
- Define recovery priorities
- Determine acceptable downtime (RTO/RPO)
- Assess third-party/vendor security risks

---

# 8. Network Security

### Network Monitoring
Capture and analyze network traffic (PCAP) for:
- Threat detection
- Forensics
- Incident investigations

### Network Segmentation
Separate networks using VLANs/PVLANs to:
- Reduce attack surface
- Limit lateral movement
- Protect sensitive systems

### Monitor Blocked Traffic
Review denied firewall traffic to identify:
- Compromised devices
- Misconfigurations
- Unauthorized applications

### Baseline & Alerting
Generate alerts for abnormal network activity, such as:
- Unusual protocols
- Unexpected destinations
- Traffic spikes
- Network saturation

---

# Key Takeaways

- Maintain an accurate asset inventory.
- Remove unsupported (EOL) systems.
- Harden systems and deploy EDR.
- Follow the **3-2-1-1-0** backup strategy.
- Protect users against phishing.
- Filter malicious web traffic.
- Patch critical vulnerabilities quickly.
- Enforce MFA and least privilege.
- Perform continuous risk assessments.
- Monitor and segment the network to detect and contain threats.