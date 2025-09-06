# Bring Your Own Device (BYOD) Security Policy Template

**Version:** 1.0  
**Effective Date:** September 6, 2025  
**Next Review Date:** September 6, 2025

## 1.0 Purpose
The goals of the [Company Name] BYOD program are to provide employees with flexibility, enhance productivity, and support mobility. This policy defines the mandatory security requirements for using a personally-owned device (smartphone, tablet, laptop) to access company resources. Its purpose is to protect corporate data on non-corporate assets by enforcing strict security controls, thereby mitigating risks of data leakage, unauthorized access, and malware infection. This policy aligns with **ISO 27001:2022 Control A.8.1 (User endpoint devices)**.

## 2.0 Scope
This policy is mandatory for any employee or contractor who wishes to access any company data (including email) from a personally-owned device.
- **Eligibility:** Participation in the BYOD program is a privilege, not a right, and is extended to all full-time employees. Contractors may be granted access on a case-by-case basis with explicit approval from the CISO.
- **Approval:** All users must formally request and receive approval before enrolling a personal device.

## 3.0 Supported Devices
- **Operating Systems:** Devices must run a supported, non-rooted/non-jailbroken operating system. This is defined as a version still receiving security updates from the manufacturer.
- **Device Health:** The device must be in good working order and have a functional camera for enrollment and a screen free of significant defects.

## 4.0 Access Control and Data Management
- **Level of Access:** Access from BYOD devices is granted based on the principle of least privilege. Users will only be able to access applications and data necessary for their role through the secure corporate container. Access to highly sensitive systems or administrative functions may be restricted or prohibited from personal devices.
- **Data Segregation:** All company data, applications, and connections **must** be contained within an encrypted, managed container installed via the company's Mobile Device Management (MDM) solution. This logically separates corporate data from personal data.
- **Data Leakage Prevention:** Users are strictly prohibited from copying, pasting, screenshotting (where disabled by the MDM), or otherwise moving data from the secure corporate container to personal apps or storage.

## 5.0 Mandatory Security Safeguards
To be eligible for the BYOD program, the device must comply with the following controls, which will be enforced by the MDM solution:
- **Use of MDM Software:** The device **must** be enrolled in the company's Mobile Device Management(MDM) solution. The MDM software is the primary tool used to implement and verify all security settings.
- **Password Protected Access:** A strong, unique passcode or biometric authentication is required to unlock both the device and the separate corporate container.
- **Keep Updated:** The device OS and all applications within the corporate container must be updated with security patches within the timeframe set by the Vulnerability Management Policy. The MDM will block access from non-compliant devices.
- **Provide Antivirus Software:** An approved Mobile Threat Defense (MTD) solution, deployed via the MDM, must be installed and active to protect against malware, phishing, and network-based attacks.
- **Manually Control Wireless Connectivity:** Users are required to disable Wi-Fi and Bluetooth services when not in use and must only connect to trusted, secure wireless networks for business purposes.
- **Enable "Find My Device":** A native device locator service (Apple's `Find My`, Google's `Find My Device`) must be enabled by the user to assist in recovery.

## 6.0 Company Rights and Permitted Activities
By enrolling a device in the BYOD program, the user consents to the following:
- **Monitoring:** The company has the right to monitor activity **within the secure corporate container only**. This includes application usage, compliance status, and network traffic originating from the container. The company cannot and will not monitor personal emails, photos, applications, or browsing history.
- **Configuration Enforcement:** The IT security team is permitted to enforce security configurations on the device as they relate to the corporate container, such as enforcing encryption, setting passcode complexity, and managing corporate applications.
- **Remote Wipe (Safeguard for Compromise):** In the event a device is lost, stolen, the employee is terminated, or the device is deemed a security risk, the company has the right to perform a **selective remote wipe** that removes only the corporate container and all data within it. This action is a primary safeguard and will be executed immediately upon notification.

## 7.0 Regulatory Compliance
All data handled on a BYOD device is subject to the same regulations as data on company-owned assets. If a user's role involves handling regulated data (PII under GDPR, PHI under HIPAA), they must adhere to all relevant data handling procedures, even on a personal device. Failure to do so is a compliance violation.

## 8.0 User Responsibilities
- **Data Backup:** The user is solely responsible for backing up their personal data (photos, contacts, files). The company is not liable for any loss of personal data for any reason, including a remote wipe event.
- **Immediate Reporting:** The user **must** report a lost, stolen, or compromised device to the IT Security department within **one hour** of discovery.

## 9.0 Enforcement
This policy is strictly enforced via the automated controls of the MDM solution. Any attempt to circumvent these controls will result in immediate revocation of all BYOD privileges and may lead to disciplinary action.
