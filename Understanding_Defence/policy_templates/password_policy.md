# Password Policy Template

**Version:** 1.0  
**Effective Date:** September 6, 2025  
**Next Review Date:** September 6, 2025  

## 1.0 Purpose
To establish rigorous, modern requirements for credential creation and management to defend against brute-force, dictionary, and credential-stuffing attacks. This policy aligns with **ISO 27001:2022 Control A.5.17** and modern NIST SP 800-63B guidelines.

## 2.0 Scope
This policy applies to all passwords and passphrases used to access any of [Company Name]'s systems, applications, and services.

## 3.0 Policy Requirements

### 3.1 Credential Construction: Passphrases over Passwords
- **Passphrases Preferred:** Users are strongly encouraged to use **passphrases** (a sequence of multiple words) instead of traditional passwords. For example, `Uche's-Last-Lecture-Was-on-Satellites-Sensing` is significantly stronger and easier to remember than `Uche@123`.
- **Minimum Length:** All credentials must have a minimum length of **15 characters**.
- **Strength and Complexity:**
  - All new credentials will be automatically screened against a database of known-compromised passwords and common dictionary words. If a match is found, the credential will be rejected.
  - Incremental or sequential changes to a password (changing `@Kumasi2024` to `@Kumasi2025`) are prohibited and will be blocked by the system.

### 3.2 Credential Management
- **Mandatory Password Manager:** The use of the company-provided enterprise password manager is **mandatory** for all employees. It must be used to generate and store unique, strong credentials for every service that requires a login. Storing credentials in browsers, text files, or spreadsheets is a direct violation of this policy.
- **Password History:** The system will prevent the reuse of the last **24** passwords.
- **Password Expiration:**
  - Standard user account passwords must be changed at least every **90 days**.
  - Privileged and administrative account passwords must be changed at least every **60 days**.
- **Initial Passwords:** All system-generated or temporary passwords must be changed by the user upon their first login. These initial passwords must be delivered to the user via a secure, out-of-band channel.

## 4.0 Enforcement
- Technical controls will be implemented to enforce all credential construction and management rules at the system level.
- An account will be temporarily locked out after **5** consecutive failed login attempts.
- Audits will be performed to ensure the password manager is being used. Deliberate non-compliance, such as writing down passwords, will result in immediate corrective action and potential disciplinary proceedings.
