# Information Security Incident Management Policy (2 in 1 Template)

**Version:** 1.0  
**Effective Date:** September 13, 2025  
**Next Review Date:** September 13, 2026  


## 1.0 Purpose

The purpose of this policy is to ensure all information security incidents are managed in a structured, efficient, and consistent manner, reducing the impact on [Company Name]'s operations and reputation, and ensuring compliance with legal, regulatory, and contractual obligations.

## 2.0 Scope

This policy applies to all employees, contractors, third parties, and systems involved in the processing, storage, or transmission of [Company Name]'s information assets.

## 3.0 Terms and Definitions

- **Incident**: An event that compromises or threatens the confidentiality, integrity, or availability of information assets.
- **Incident Response Team (IRT)**: A designated group of personnel trained and responsible for handling information security incidents.
- **Containment**: Steps taken to limit the impact and spread of an incident.
- **Eradication**: Actions to eliminate the root cause and artifacts of an incident.
- **Recovery**: Procedures to restore affected systems and operations to a secure state.
- **Lessons Learned**: Systematic review and documentation of the incident to prevent recurrence.
- **SIEM**: Security Information and Event Management system, used for real-time analysis of security alerts.


## 3.0 Policy Requirements

### 3.1 Four-Step Incident Response Cycle

The classic model for incident management comprises four essential phases:

#### 1. Preparation
- Maintain detailed, up-to-date incident response plans and playbooks.
- Conduct regular security awareness training and incident response drills for all employees.
- The IRT must be equipped and trained with appropriate response tools and resources.

#### 2. Detection and Analysis
- All personnel must immediately report suspected or actual security incidents via established channels (hotline, email, ticket system).
- Automated systems (SIEM, IDS/IPS, EDR) must continuously monitor and alert on suspicious activity.
- The IRT triages, categorizes, and documents incidents, determining severity and scope.

#### 3. Containment, Eradication, and Recovery
- **Containment:** The IRT isolates affected systems, disables compromised accounts, blocks malicious traffic, and prevents escalation.
- **Eradication:** Remove malware, unauthorized access, or vulnerabilities; conduct root cause analysis.
- **Recovery:** Restore systems from clean backups, patch vulnerabilities, and monitor for recurrence. If a user credential is involved, there should be a password reset and account hardening measures. Resume business operations once confirmed safe.

#### 4. Post-Incident Activity (Lessons Learned)
- The IRT conducts a formal post-mortem for significant incidents within seven days of closure.
- Document findings, lessons learned, and assign and track corrective actions to completion.

---

### 3.2 Six-Step Incident Response Cycle

For greater granularity and control, organizations may adopt the six-step model:

#### 1. Preparation
- Develop, document, and test incident response plans and playbooks, tailored to likely scenarios.
- Conduct annual training and exercises for the IRT and all relevant personnel.

#### 2. Identification
- Promptly report all suspicious activities or events.
- The IRT uses automated tools and manual analysis to confirm, document, and classify incidents.

#### 3. Containment
- Swiftly isolate systems or networks to limit the scope and damage of the incident.
- Apply temporary controls to prevent further spread.

#### 4. Eradication
- Remove threats, malware, and unauthorized accounts.
- Perform forensic analysis to ensure all traces are eliminated and root causes are addressed.

#### 5. Recovery
- Restore systems and services to normal operation from clean backups.
- Closely monitor for signs of re-infection or persistence.

#### 6. Lessons Learned
- Within seven days of incident closure, conduct a formal review.
- Document what happened, what was done well, what could be improved, and implement agreed improvements.

---

### 3.3 Notes on 4-Step vs. 6-Step Cycles
I honestly know you're asking yourself why I included both Policy statements :) Well, I did that just so that I could recall for both and when the need be, I could easily choose which one to go in for an engagement. 

- The **4-step cycle** is a streamlined approach combining containment, eradication, and recovery, suitable for smaller organizations or less complex incident environments.
- The **6-step cycle** separates identification, containment, eradication, and recovery for organizations seeking more detailed process control and clarity in roles, handoffs, and documentation, especially in regulated or high-risk environments.
- The purpose is to select for an organization, the model that best fits their risk profile, complexity, and regulatory obligations.

---

## 4.0 Roles and Responsibilities

- **Chief Information Security Officer (CISO):**
  - Owns the incident management program.
  - Approves the policy and major incident communications.
  - Ensures regulatory, legal, and contractual notifications are made.
- **Incident Response Team (IRT):**
  - Leads all phases of incident response.
  - Maintains plans, playbooks, and readiness.
  - Coordinates containment, eradication, and recovery activities.
  - Conducts post-incident reviews and tracks remediation.
- **IT Security Team:**
  - Monitors systems, detects and escalates alerts, supports the IRT with technical expertise.
- **Department Managers:**
  - Cooperate with IRT, provide system access and business context, implement recommended changes.
- **All Employees, Contractors, and Vendors:**
  - Must immediately report any suspected or confirmed information security incidents.
  - Participate in incident investigations as required and follow communication protocols.

---

## 5.0 Compliance

- Compliance with this policy is mandatory.
- Failure to follow reporting, response, or participation requirements may result in disciplinary action.
- The policy supports compliance with ISO/IEC 27001:2022 A.5.24–A.5.28, and all applicable regulatory and contractual requirements.

---

## 6.0 Review

This policy will be reviewed annually, after any significant changes to the incident response process, or following major incidents, to ensure ongoing suitability and effectiveness.
