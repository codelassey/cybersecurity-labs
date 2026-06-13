# Risk Register

## Operational Environment

The organization operates a hybrid IT environment consisting of on-premise systems and cloud services. 
Employees access business applications remotely, and critical assets include customer databases, email systems, endpoints, and internal servers.

The organization must protect sensitive information, maintain service availability, and prevent unauthorized access.

---

| Asset | Risk(s) | Description | Likelihood | Severity | Priority |
|---|---|---|---|---|---|
| Employee Accounts | Phishing Attack | Employees may be tricked into providing credentials through malicious emails, leading to unauthorized account access. | 3 | 3 | 9 |
| Customer Database | Data Breach | Weak access controls or vulnerabilities may allow attackers to access sensitive customer information. | 2 | 3 | 6 |
| Web Application | SQL Injection | Poor input validation may allow attackers to manipulate database queries and access restricted data. | 2 | 3 | 6 |
| Internal Network | Malware Infection | A compromised endpoint may introduce malware into the organization network. | 2 | 2 | 4 |
| Backup Systems | Ransomware Impact | Attackers may encrypt backup files, preventing recovery after a ransomware attack. | 2 | 3 | 6 |
| Email System | Business Email Compromise | Attackers may impersonate employees to perform fraud or steal confidential information. | 3 | 2 | 6 |
| Remote Access Services | Unauthorized Access | Weak authentication controls may allow attackers to gain remote access. | 2 | 3 | 6 |

---

# Notes

The highest priority risks are those involving unauthorized access, phishing, and data exposure because they can directly impact confidentiality, integrity, and availability of organizational resources.

Recommended controls include:

- Multi-factor authentication (MFA)
- Regular vulnerability assessments
- Security awareness training
- Strong access controls
- Continuous monitoring and logging
- Regular backup testing


# Risk Scoring

Likelihood:
- 1 = Rare
- 2 = Possible
- 3 = Likely


Severity:
- 1 = Low Impact
- 2 = Moderate Impact
- 3 = High Impact


Risk Priority Formula:

**Likelihood × Severity = Risk Score**

| Likelihood | Low (1) | Moderate (2) | Catastrophic (3) |
|---|---|---|---|
| **Certain (3)** | 3 | 6 | 9 |
| **Likely (2)** | 2 | 4 | 6 |
| **Rare (1)** | 1 | 2 | 3 |


## Risk Rating

| Score | Risk Level | Action |
|---|---|---|
| 1-2 | Low | Monitor and review periodically |
| 3-4 | Moderate | Implement security improvements |
| 6 | High | Prioritize remediation actions |
| 9 | Critical | Immediate action required |
