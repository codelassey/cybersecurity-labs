# Building a Home SOC Lab

This guide walks through building a basic home SOC lab consisting of:

- **pfSense Firewall**
- **Windows Server 2022 Active Directory**
- **Windows 10 Workstation**
- **Sysmon**
- **CrowdSec**

---

# Lab Architecture

```text
                Internet
                    │
              WAN (Red Adapter)
                    │
              +----------------+
              |    pfSense     |
              +----------------+
             /                  \
    LAN (Green)            DMZ (Orange)
         │                      │
         │                      │
+----------------+      Future Public Servers
| Active Directory |
| Windows Server   |
+----------------+
         │
         │
+----------------+
| Windows 10     |
| Workstation    |
+----------------+
```

---

# 1. pfSense Firewall

## Download

- Download the latest **pfSense Community Edition** ISO from the official website.

---

## VirtualBox Configuration

Create a new VM and configure **3 network adapters**:

| Adapter | Purpose | 
|----------|----------|
| Adapter 1 | WAN (External Network) | 
| Adapter 2 | LAN (Internal Network) | 
| Adapter 3 | DMZ | 

> **Important:** Record each adapter's **MAC address** to identify interfaces during installation.

---

## Installation

- Boot using the pfSense ISO.
- Accept the license.
- Select **Install**.
- Choose keyboard layout.
- Use the default partition scheme.
- Skip:
  - Mirror setup
  - Disk encryption
  - Manual configuration
- Reboot after installation.

---

## Initial Configuration

### Assign Interfaces

Select:

- WAN → Red adapter
- LAN → Green adapter
- DMZ → Orange adapter

Confirm the configuration.

---

### Configure IP Addresses

#### WAN

- Configure using **DHCP**

#### LAN

Assign:

- Static IP
- Preferred subnet
- **Gateway = None**

This LAN IP becomes the gateway for internal hosts.

---

# 2. Active Directory Server

## Download

Download:

- Windows Server 2022 Evaluation ISO
- 64-bit
- English version

---

## VirtualBox Configuration

Create a VM.

Network:

- Internal Network
- Connected to **GREEN (LAN)**

---

## Install Windows Server

Install:

- Windows Server 2022 Datacenter Evaluation
- Desktop Experience (GUI)

Installation steps:

- English language
- Accept license
- Custom installation
- Install on default disk
- Set Administrator password

---

## Configure Windows Server

After login:

Configure IPv4:

- Static IP
- Correct subnet
- Gateway = pfSense LAN IP

Verify connectivity by pinging the gateway.

Rename the server.

Restart.

---

# Install Active Directory Domain Services (AD DS)

Using Server Manager:

1. Add Roles and Features
2. Role-based installation
3. Select server
4. Install:
   - Active Directory Domain Services
5. Promote server to Domain Controller

Configuration:

- Create a new forest
- Set DSRM password
- Leave DNS defaults
- Verify NetBIOS name
- Leave database paths default
- Install and reboot

---

# Populate Active Directory

Use **BadBlood** to generate a realistic AD environment.

Steps:

- Download BadBlood
- Extract files
- Open PowerShell as Administrator
- Navigate to BadBlood folder
- Execute:

```powershell
Invoke-BadBlood.ps1
```

Creates:

- ~2500 users
- ~500 groups
- Organizational Units (OUs)
- ~100 computers
- Various intentional AD misconfigurations

Ideal for:

- Blue Team labs
- BloodHound
- Detection engineering
- Threat hunting

---

# 3. Windows 10 Workstation

## Download

Use Microsoft's Media Creation Tool to create a Windows 10 ISO.

---

## VirtualBox Configuration

Network:

- Internal Network (GREEN)

---

## Windows Installation

Install:

- Windows 10 Pro
- English

During setup:

- Skip product key
- Custom installation
- Limited setup (offline account)
- Create local administrator
- Disable unnecessary telemetry/privacy options

---

## Configure Networking

Configure IPv4:

- Static IP
- Gateway = pfSense LAN IP

Rename the workstation.

Restart.

---

# Join the Domain

Open:

```
Advanced System Settings
```

Select:

```
Change
```

Join:

- Domain
- Enter NetBIOS domain name

Authenticate using:

```
Domain Administrator
```

Restart.

---

# 4. Sysmon

Sysmon provides detailed Windows endpoint logging.

---

## Download

Download:

- Sysinternals Sysmon
- Sysmon XML configuration file

---

## Installation

Open PowerShell as Administrator.

Run:

```powershell
sysmon.exe -accepteula -i YOURFILE.xml
```

Sysmon begins generating enhanced Windows Event Logs.

Typical events include:

- Process creation
- Network connections
- Registry modifications
- Driver loads
- Image loads
- File creation
- DNS queries (depending on configuration)

---

# 5. CrowdSec

CrowdSec provides collaborative threat detection and optional automated blocking.

---

## Create Account

- Register for a free CrowdSec account.

---

## Install on Linux

Follow the installation instructions provided in the CrowdSec dashboard.

---

## Install on Windows

Download:

- CrowdSec Windows MSI installer from GitHub.

Install on:

- Active Directory Server
- Windows Workstation

---

## Configure Windows Collection

Open PowerShell as Administrator.

Install Windows Firewall collection:

```powershell
.\cscli collections install crowdsecurity/windows-firewall
```

Edit:

```
C:\ProgramData\CrowdSec\config\acquis.yaml
```

Add the required acquisition configuration.

Restart the system.

---

# Windows Firewall Bouncer (Optional)

For automatic IP blocking:

Download and install:

- Windows Firewall Bouncer bundle

This enables CrowdSec to automatically block malicious IP addresses using Windows Firewall.

---

# Final Lab Components

| Component | Purpose |
|-----------|---------|
| pfSense | Firewall, routing, network segmentation |
| Windows Server 2022 | Active Directory Domain Controller |
| BadBlood | Generate realistic AD environment |
| Windows 10 | Domain-joined endpoint |
| Sysmon | Advanced Windows telemetry |
| CrowdSec | Threat detection and automated blocking |

---

# Learning Objectives

For me, I have already deployed these and even more in my persanal lab environment. By completing this lab, 
you can practice:

- Firewall administration
- Network segmentation
- Active Directory deployment
- Domain administration
- Windows endpoint management
- Sysmon log analysis
- Threat detection
- CrowdSec deployment
- Blue Team investigations
- Detection engineering
- Threat hunting
- SOC workflows
- Incident response
- Windows event logging
- Active Directory attack simulations
```