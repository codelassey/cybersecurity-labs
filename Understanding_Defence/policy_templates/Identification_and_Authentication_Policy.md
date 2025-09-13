# Identification and Authentication Policy (Template)

**Version:** 1.0  
**Effective Date:** September 13, 2025  
**Next Review Date:** September 13, 2026

## 1.0 Purpose
To establish robust procedures for uniquely identifying users and systems and verifying their identities before granting access to information assets, in accordance with ISO/IEC 27001:2022 Controls A.5.16 and A.5.17.

## 2.0 Scope
This policy applies to all users and systems requiring access to [Company Name's] information assets.

## 3.0 Terms and Definitions

- **Authentication**: The process of verifying the identity of a user, device, or system.
- **Identity Management**: Processes and technologies for managing the lifecycle of digital identities.
- **Multi-Factor Authentication (MFA)**: Authentication using two or more independent credentials.
- **Service Account**: Non-human account used by applications or services, not individuals.
- **Passphrase**: A sequence of words or text used for authentication, typically longer and more secure than a traditional password.
- **Password Manager**: A software solution for securely storing and managing user credentials.
- **FIDO2 security key**: A physical hardware device for two-factor and multifactor authentication.

## 4.0 Policy Requirements

### 4.1 Unique Identification
- Every user and system must have a unique identifier; shared or generic accounts require CISO approval and must be non-interactive where possible.

### 4.2 Lifecycle Management
- **Onboarding**: Accounts created after documented request and approval.  
- **Role Change**: Access rights reviewed and updated within 12 hours of a role change.  
- **Offboarding**: All access revoked by end of employment or contract or immediately for involuntary terminations.
- **Reviews**: IT and managers conduct quarterly reviews; privileged accounts are reviewed every 3 weeks.

### 4.3 Authentication
- **MFA**: Mandatory for remote, cloud, and privileged access.  
- **Preferred**: FIDO2 hardware keys; **Permitted**: Authenticator apps; **Prohibited**: SMS for privileged/sensitive access.
- **Passphrases**: Minimum of 15 characters, avoid dictionary words or personal info, screened against breach lists.
- **Password Manager**: Use is mandatory for all employees and contractors.
- **Service Accounts**: Unique, non-interactive, stored in credential vault, rotated at least annually.

## Roles and Responsibilities

- **Chief Information Security Officer (CISO)**: Approves this policy, oversees implementation, and manages exceptions.
- **IT Security Team**: Implements technical controls, performs regular account reviews, and responds to identity-related incidents.
- **HR Managers**: Approve new user access, report role changes, and participate in quarterly access reviews.
- **System Owners**: Ensure only authorized identities have access to their systems.
- **All Employees and Contractors**: Protect their credentials, use only assigned accounts, complete training, and report suspicious activity.

## 4.0 Enforcement
Violations such as account sharing or weak credentials will result in disciplinary action. Technical controls and audits will enforce compliance.

## 5.0 Review
This policy is to be reviewed annually or with significant business or regulatory change.
