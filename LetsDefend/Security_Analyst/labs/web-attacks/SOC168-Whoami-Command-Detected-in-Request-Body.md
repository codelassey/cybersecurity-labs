# SOC168 Analysis: Whoami Command Detected in Request Body

## Alert Overview

| Field | Value |
|-------|-------|
| **Alert Name** | SOC168 - Whoami Command Detected in Request Body |
| **Event ID** | 118 |
| **Event Time** | February 28, 2022, 04:12 AM |
| **Severity/Level** | Security Analyst |
| **Hostname** | WebServer1004 |
| **Source IP** | 61.177.172.87 |
| **Destination IP** | 172.16.17.16 |
| **Protocol** | HTTP |
| **Method** | POST |
| **Requested URL** | `https://172.16.17.16/video/` |
| **Alert Trigger** | Request body contains `whoami` string |
| **Device Action** | Allowed |

![alert_details](imaages/soc21.png)

---

# Investigation Summary

I started by creating a case from the alert so I could begin following the investigation 
playbook.

Looking at the alert details, one thing immediately stood out to me: the request body contained the command **`whoami`**. That command is commonly used by attackers after they've found a way to execute commands on a target system because it tells them which user account their commands are running as.

Since the request was a **POST** request directed at the `/video/` endpoint on **WebServer1004**, my initial thought was that this could be an attempt to exploit a **Command Injection** vulnerability.

Before assuming the attack had succeeded, I wanted to understand the full sequence of events, so I moved into the log management platform.

---

# Log Analysis

To gain a broader picture of the activity, I filtered the logs using the source IP address **61.177.172.87**.

My goal was to determine whether the attacker was targeting multiple systems or focusing on a single host.

![log_analysis](imaages/soc22.png)

The search returned **five events** occurring between **04:11 AM and 04:15 AM** on **February 28, 2022**.

All communication was directed exclusively at **WebServer1004 (172.16.17.16)**.

After expanding each event, I reconstructed the attack timeline.

| Time | POST Parameter | HTTP Status | Response Size |
|------|----------------|------------|---------------|
| 04:11 | `?c=ls` | 200 | 1021 bytes |
| 04:12 | `?c=whoami` | 200 | 912 bytes |
| 04:13 | `?c=uname` | 200 | 910 bytes |
| 04:14 | `?c=cat /etc/passwd` | 200 | 1321 bytes |
| 04:15 | `?c=cat /etc/shadow` | 200 | 1501 bytes |

As I reviewed the requests, the attack progression became very obvious.

The attacker wasn't sending random commands but they were following a logical post-exploitation workflow.

They began by listing directory contents using:

```text
ls
```

Next, they identified the execution context using:

```text
whoami
```

Then they gathered system information:

```text
uname
```

After confirming command execution worked, they escalated to reading sensitive operating system files:

```text
cat /etc/passwd
```

followed immediately by:

```text
cat /etc/shadow
```

What immediately caught my attention was that **every request returned HTTP 200 (OK)**.

Even more importantly, the **response sizes changed with each command**.

That strongly suggested the application was returning the output of the executed commands back to the attacker.

At this point, I already suspected that the attack had successfully achieved remote command execution.

---

# Endpoint Investigation

Although the HTTP responses strongly suggested successful command execution, I wanted direct confirmation from the endpoint itself.

I switched my focus to the **Endpoint Security** dashboard and reviewed the terminal history for **WebServer1004**.

The terminal history confirmed exactly what I suspected.

![endpoint](imaages/soc23.png)

The same commands observed in the HTTP requests appeared in the operating system's command history, and the timestamps aligned perfectly with those recorded in the web logs.

Seeing both data sources agree gave me high confidence that the attacker had successfully executed commands on the underlying operating system.

This confirmed that the incident was no longer just an attempted exploitation—it was an actual compromise.

---

# Immediate Containment

At this stage, my priority shifted from investigation to containment.

Since the attacker had successfully gained command execution on the server, I believed it was important to isolate the affected host to prevent any further interaction.

Although containment can temporarily impact service availability, limiting the attacker's access takes priority once a system is confirmed to be compromised.

I therefore initiated containment of **WebServer1004** through the Endpoint Security platform.

My assumption was that the organization would have redundancy or failover mechanisms in place to minimize any service disruption while the compromised host was isolated.

---

# Threat Intelligence

With containment underway, I gathered additional intelligence about the attacking IP address.

## WHOIS

![whois](imaages/soc24.png)

WHOIS identified the source IP as belonging to:

- **CHINANET Jiangsu Province Network**
- Public IP address
- China

## VirusTotal

![virustotal](imaages/soc25.png)

I then checked the IP address on **VirusTotal**.

The results showed:

- Flagged as malicious by multiple security vendors
- Community reports linked the IP to SSH brute-force activity

This external intelligence supported what I had already observed during the investigation and reinforced my confidence that the activity was malicious.

---

# Examination of HTTP Traffic

Reviewing the HTTP traffic showed a clear command injection pattern.

The attacker attempted to execute multiple operating system commands directly through the POST parameter:

```text
ls

whoami

uname

cat /etc/passwd

cat /etc/shadow
```

Unlike reconnaissance attempts seen in previous investigations, these requests returned successful responses containing varying amounts of data.

Combined with the endpoint evidence, this confirmed that the application accepted user input and executed it on the operating system.

The attack was therefore classified as a **successful Command Injection**.

---

# Determining Whether the Traffic Was Malicious

By this point, I had several independent indicators confirming the malicious nature of the activity.

These included:

- Execution of operating system commands
- Successful HTTP 200 responses
- Response sizes changing according to the command executed
- Matching command history on the endpoint
- Malicious IP reputation
- Attempts to access sensitive Linux files

Taken together, there was no reasonable explanation other than a genuine compromise.

---

# Verification of Planned Testing

Although the attack already appeared legitimate, I still wanted to verify that it wasn't part of an authorized penetration test.

![email](imaages/soc26.png)

I searched the email records for notifications regarding planned assessments before **February 2022**, focusing on:

- WebServer1004
- The destination IP
- The source IP
- Keywords such as **pentest**

I also remembered investigating SQL Injection and XSS alerts around the same timeframe, so I wanted to make sure this wasn't part of a coordinated internal exercise.

No matching emails were found.

There was no evidence that this activity had been authorized.

I therefore concluded that this was **not a planned penetration test**.

---

# Traffic Direction

```text
Internet -> Company Network
```

The attack originated from a public IP address and targeted an internal web server.

---

# Attack Success Assessment

Earlier in the investigation, I had already suspected that the attack had succeeded based on the HTTP responses.

The endpoint investigation removed any remaining doubt.

The evidence included:

- HTTP 200 responses for every command
- Different response sizes corresponding to command output
- Matching command history on the endpoint
- Successful execution of sensitive commands such as:

```text
cat /etc/passwd

cat /etc/shadow
```

These findings confirmed that the attacker successfully executed commands on the server.

Unlike the previous SQL Injection and XSS investigations, this attack resulted in an actual compromise of the target system.

---

# Indicators of Compromise (IOCs)

| Type | Value |
|------|-------|
| Source IP | 61.177.172.87 |
| Destination IP | 172.16.17.16 |
| Hostname | WebServer1004 |
| Attack Type | Command Injection |
| Target Endpoint | `/video/` |
| Commands Observed | `ls`, `whoami`, `uname`, `cat /etc/passwd`, `cat /etc/shadow` |

---

# Tier 2 Escalation Assessment

Unlike the previous web attack investigations, this incident clearly required escalation.

The attacker successfully executed commands on the operating system and accessed sensitive system files.

Because the server had been confirmed as compromised, additional incident response activities such as forensic acquisition, root cause analysis, credential review, and recovery would be required.

I therefore escalated the incident to **Tier 2** for further investigation.

---

# Findings

| Investigation Item | Result |
|--------------------|--------|
| Was the alert legitimate? | Yes |
| Attack Type | Command Injection |
| Traffic Direction | Internet → Company Network |
| Was it a planned test? | No |
| Was the attack successful? | Yes |
| HTTP Response | 200 OK |
| Endpoint Evidence | Command execution confirmed |
| Device Contained | Yes |
| Tier 2 Escalation Required | Yes |
| Case Classification | True Positive |

---

# Conclusion

The investigation revealed that **WebServer1004** was successfully exploited through a **Command Injection vulnerability** exposed by the `/video/` endpoint.

Reviewing the HTTP logs showed the attacker systematically executing operating system commands, beginning with basic reconnaissance (`ls`, `whoami`, `uname`) before progressing to attempts to access sensitive Linux files (`/etc/passwd` and `/etc/shadow`). Every request returned **HTTP 200** responses with varying response sizes, strongly indicating that command output was being returned to the attacker.

To validate this finding, I reviewed the endpoint's terminal history. The operating system logs confirmed that the same commands had been executed at the exact timestamps observed in the web logs. This correlation confirmed that the attacker had achieved remote command execution on the server.

Threat intelligence further showed that the source IP belonged to the **CHINANET Jiangsu Province Network** and had been reported for previous malicious activity, including SSH brute-force attacks. Email records showed no evidence that this activity was part of an authorized penetration test.

Given the confirmed compromise, I initiated containment of the affected server to prevent further interaction with the attacker and escalated the incident to **Tier 2** for continued incident response and forensic investigation.

The incident was classified as a **True Positive** involving a **successful Command Injection attack**.

---