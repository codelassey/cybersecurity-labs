# Phishing Email Analysis

## Overview

**Phishing** is a social engineering attack that tricks users into revealing sensitive information or executing malicious content. It is one of the most common **initial access vectors** used by attackers and corresponds to the **Delivery** phase of the Cyber Kill Chain.

### Common Objectives

- Steal credentials
- Deliver malware
- Gain initial access
- Exploit human trust

### Common Lures

- "You've won a prize"
- "Your account will be suspended"
- "Limited-time discount"
- Fake invoices
- Password reset notifications

---

# Information Gathering

Attackers often gather publicly available information before launching phishing campaigns.

Common sources include:

- Company websites
- LinkedIn
- Social media
- Pastebin
- OSINT tools (e.g., **theHarvester**)

---

# Email Spoofing

Email spoofing is the practice of forging the sender's email address to make an email appear legitimate.

### Email Authentication Protocols

| Protocol | Purpose |
|----------|---------|
| SPF | Verifies authorized mail servers for a domain |
| DKIM | Verifies message integrity using digital signatures |
| DMARC | Defines how SPF/DKIM failures should be handled |

### Validation Tools

- MXToolbox
- Whois
- DNS lookup tools

> **Note:** Even if SPF, DKIM, and DMARC pass, an email may still be malicious if a legitimate account has been compromised.

---

# Email Traffic Analysis

Useful indicators for identifying phishing campaigns:

- Sender email address
- SMTP IP address
- Sender domain
- Subject line
- Recipient(s)
- Timestamp

Tracking these indicators helps determine:

- Scope of the campaign
- Targeted users
- Reused infrastructure

---

# Email Header Analysis

## What is an Email Header?

An email header contains metadata about how an email was transmitted.

### Important Header Fields

| Field | Description |
|--------|-------------|
| From | Sender's email address |
| To | Recipient |
| CC/BCC | Additional recipients |
| Date | Time email was sent |
| Subject | Email topic |
| Return-Path / Reply-To | Address used when replying |
| Message-ID | Unique email identifier |
| MIME-Version | Email content format |
| Received | Mail server path |
| DKIM | Email signature |
| SPF | Sender validation |
| X-Spam-Status | Spam score |

---

# Accessing Email Headers

### Gmail

1. Open email
2. Click **⋮**
3. Select **Download message**
4. Open the `.eml` file

### Outlook

```
File → Info → Properties → Internet Headers
```

---

# Header Analysis Checklist

## 1. Verify SMTP Server

Questions:

- Did the email originate from the legitimate mail server?
- Does the SMTP IP belong to the sender's organization?

Useful tools:

- MXToolbox
- Whois
- DNS MX lookup

---

## 2. Compare "From" vs "Reply-To"

Normally these should match.

Different addresses may indicate:

- Business Email Compromise (BEC)
- Reply redirection
- Spoofing

> This alone is **not** proof of phishing. Consider the email as a whole.

---

# Static Analysis

Static analysis examines an email without executing its content.

### Inspect

- URLs
- Attachments
- HTML source
- Domain age
- Sender reputation

### URL Checks

- Hover over hyperlinks
- Compare displayed URL with actual destination
- Check for typosquatting

Example:

```
Displayed:
https://google.com

Actual:
https://google-login.example.com
```

---

## Domain Reputation

Check URLs using:

- VirusTotal
- Cisco Talos Intelligence
- AbuseIPDB

Things to verify:

- Detection rate
- Last analysis date
- Domain age
- IP reputation

> VirusTotal results may be outdated. Consider reanalyzing suspicious URLs.

---

# Attachment Analysis

Before opening attachments:

- Scan with antivirus
- Check file hashes
- Perform static analysis
- Review file metadata

Dynamic analysis is generally faster for understanding behavior.

---

# Dynamic Analysis

Never execute suspicious files on your host machine.

Instead, use:

- Virtual machines
- Sandboxes
- Isolated browsers

### Online Browsers

- Browserling

### Malware Sandboxes

- Any.Run
- Hybrid Analysis
- Joe Sandbox
- VMRay

---

# Dynamic Analysis Checklist

Observe:

- Process creation
- Network connections
- File modifications
- Registry changes
- Persistence mechanisms
- Dropped files

Allow enough execution time because malware may delay execution to evade detection.

---

# URL Safety Tips

Before opening a phishing URL:

- Inspect URL parameters
- Remove personal identifiers (email addresses, tokens)
- Avoid validating your email address for attackers

Example:

```
https://site.com/login?email=user@example.com
```

Visiting such a URL confirms that the email address is active.

---

# Investigation Workflow

```text
Receive Email
      │
      ▼
Review Headers
      │
      ▼
Verify SMTP Server
      │
      ▼
Compare From vs Reply-To
      │
      ▼
Inspect URLs & Attachments
      │
      ▼
Check Reputation
(VirusTotal / Talos / AbuseIPDB)
      │
      ▼
Static Analysis
      │
      ▼
Dynamic Analysis (Sandbox)
      │
      ▼
Determine Verdict
```

---

# Key Takeaways

- Phishing is the most common initial access technique.
- Email spoofing can be detected using SPF, DKIM, and DMARC.
- Analyze email headers before interacting with content.
- Verify sender infrastructure using MX records and Whois.
- Compare **From** and **Reply-To** fields.
- Hover over links before clicking.
- Check URL and IP reputation using VirusTotal, Talos, and AbuseIPDB.
- Analyze suspicious files and URLs in a sandbox.
- A lack of attachments or links **does not guarantee** an email is safe.
```
