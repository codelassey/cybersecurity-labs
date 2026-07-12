# Analysis of Web Server Access Log - Cross-Site Scripting (XSS) Attack Investigation

I opened the [access log](logs/xss-apache-access-logs.txt) with a text editor and began analyzing the HTTP requests to 
identify the XSS attack timeline and attacker details. The log showed a complete attack chain against the DVWA instance.

---

## Key Findings

### Attack Timeline
**Exploitation Date:** 01/Mar/2022:08:53:20

The exploitation phase began on March 1, 2022 at 08:53:20 when the attacker successfully executed the first XSS payload by 
injecting `<script>alert(1)</script>` into the `name` parameter of the `/dvwa/vulnerabilities/xss_r/` endpoint. 
This confirmed the presence of a reflected XSS vulnerability.

### Attacker Identification
**Attacker IP Address:** 192.168.31.183

The attacker used a browser that did not specify a User-Agent string in the initial reconnaissance requests, but later requests 
showed consistent patterns from the same IP address.

### Attack Success
**Was the attack successful?** Y

The attacker successfully executed multiple XSS payloads including alert popups and cookie theft attempts.

### Attack Classification
**Type of XSS:** Reflected XSS

The attacker used reflected XSS techniques where the payload was delivered through the URL parameter and executed 
immediately in the victim's browser upon visiting the crafted link.

---

## Detailed Attack Timeline

| Time | Activity | IP Address |
|------|----------|------------|
| 08:52:31 | Initial access to DVWA | 192.168.31.183 |
| 08:52:41 | Accessed XSS reflected vulnerability page | 192.168.31.183 |
| 08:52:54 | Tested empty `name` parameter | 192.168.31.183 |
| 08:52:58 | Tested with benign value "hacker" | 192.168.31.183 |
| 08:53:06 | Tested with benign value "john" | 192.168.31.183 |
| 08:53:20 | **First malicious payload:** `<script>alert(1)</script>` | 192.168.31.183 |
| 08:54:34 | **Cookie theft attempt:** `<script>alert(document.cookie)</script>` | 192.168.31.183 |
| 08:55:08 | **Redirection attempt:** `<script>location.href='http://malicioussite.com'</script>` | 192.168.31.183 |

---

## Payload Analysis

### Reconnaissance Payloads
The attacker first tested the application with benign inputs:
- `name=`
- `name=hacker`
- `name=john`

These requests returned responses of sizes 4224, 4247, and 4245 bytes respectively, establishing baseline behavior.

### Confirmation Payload
**Payload:** `<script>alert(1)</script>`
**URL Encoded:** `%3Cscript%3Ealert%281%29%3C%2Fscript%2F`
**Response Size:** 4266 bytes (slightly larger than baseline, confirming execution)

### Cookie Theft Payload
**Payload:** `<script>alert(document.cookie)</script>`
**URL Encoded:** `%3Cscript%3Ealert%28document.cookie%29%3C%2Fscript%3E`
**Response Size:** 4280 bytes
**Purpose:** Attempted to exfiltrate session cookies that could enable session hijacking

### Redirection Payload
**Payload:** `<script>location.href='http://malicioussite.com'</script>`
**URL Encoded:** `%3Cscript%3Elocation.href%3D%27http%3A%2F%2Fmalicioussite.com%27%3C%2Fscript%3E`
**Response Size:** 4298 bytes
**Purpose:** Attempted to redirect victims to a malicious website for further exploitation (phishing, malware delivery)


---

## Conclusion

The investigation confirms a successful **Reflected XSS attack** on 01/Mar/2022 conducted from IP address **192.168.31.183**. 
The exploitation began at **08:53:20** when the attacker confirmed the vulnerability using a script alert payload. 
The attacker then escalated to more dangerous payloads including cookie theft and malicious redirection attempts. The 
vulnerable application reflected the injected script in the HTTP response, confirming the reflected nature of the attack.

