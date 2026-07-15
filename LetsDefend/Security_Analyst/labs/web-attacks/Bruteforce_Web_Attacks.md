# Analysis of Web Server Access Log - Brute Force Attack Investigation

[LOG FILE HERE](logs/brute-forcing-access.log)

---

## Key Findings

### Attack Timeline
**Successful Login Date:** 26/Apr/2023 21:44:03

The attacker successfully logged in on April 26, 2023 at 21:44:03 after conducting an extensive brute force attack against the `/login` endpoint. The successful login was followed by a POST request to `/login` that returned a 302 redirect (indicating successful authentication), compared to previous attempts that returned 200 OK (indicating failed login attempts).

### Attacker Identification
**Attacker IP Address:** 146.241.73.240

The attacker used Windows NT 10.0 with Chrome 112.0.5615.50.

### Attacker User Agent
**User Agent:** `Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/112.0.5615.50 Safari/537.36`

### Attack Success
**Was the attack successful?** Y

The attacker successfully gained access to the application after multiple login attempts.

---

## Attack Pattern Analysis

The attacker followed a methodical approach:

1. **Initial Reconnaissance (26/Apr/2023 21:33:38):** Navigated to the application homepage and loaded resources
2. **Post Enumeration (26/Apr/2023 21:33:44 - 21:33:54):** Enumerated posts 4, 5, 7, 9
3. **Comment Spamming (26/Apr/2023 21:44:18 - 21:44:41):** Attempted to spam comments on postId=3
4. **Brute Force Attack (26/Apr/2023 21:42:36 - 21:44:03):** Conducted repeated login attempts against `/login` endpoint
5. **Successful Login (26/Apr/2023 21:44:03):** Successful authentication with 302 redirect
6. **Post-Exploitation (26/Apr/2023 21:44:23 - 21:44:33):** Enumerated posts 1-10 after successful login

---

## Detailed Attack Timeline

### Pre-Attack Activity (26/Apr/2023)

| Time | Activity | IP Address |
|------|----------|------------|
| 21:33:38 | Homepage access | 66.48.84.188 |
| 21:33:44 | GET /post?postId=5 | 66.48.84.188 |
| 21:33:48 | GET /post?postId=4 | 66.48.84.188 |
| 21:33:51 | GET /post?postId=9 | 66.48.84.188 |
| 21:33:54 | GET /post?postId=7 | 61.14.246.6 |

### Brute Force Attack Phase (26/Apr/2023)

The attacker conducted multiple login attempts against `/login`:

| Time Range | Number of Attempts | Response Status |
|------------|-------------------|-----------------|
| 21:42:36 - 21:42:57 | ~20+ attempts | 200 OK |
| 21:42:57 - 21:43:30 | ~30+ attempts | 200 OK |
| 21:43:30 - 21:44:03 | ~20+ attempts | 200 OK |
| 21:44:03 | **Successful login** | **302 Redirect** |

### Comment Spam Attack (26/Apr/2023 21:44:18 - 21:44:41)

| Time | Activity | Response |
|------|----------|----------|
| 21:44:18 | POST /post/comment (postId=3) | 302 |
| 21:44:18-21:44:41 | Multiple repeated POST requests | 302 |
| 21:44:23-21:44:33 | GET /post?postId=1-10 enumeration | 200 |

---

## Payload Analysis

### Brute Force Attack Characteristics

1. **Repeated POST Requests:** The attacker sent numerous POST requests to `/login` with varying credentials
2. **Consistent Response Size:** Failed attempts returned 947 bytes, successful attempts returned 302 redirect
3. **High Frequency:** Multiple attempts per second during peak periods
4. **No Rate Limiting:** The application did not implement rate limiting or account lockout

### Evidence of Successful Login

| Indicator | Failed Attempt | Successful Attempt |
|-----------|---------------|-------------------|
| HTTP Status | 200 OK | 302 Redirect |
| Response Size | 947 bytes | 0 bytes (redirect) |
| Response Type | Login page returned | Redirect to authenticated page |

### Post-Success Activity

After successful authentication, the attacker:
1. Enumerated all posts (postId=1-10)
2. Continued spamming comments on postId=3
3. Conducted multiple comment POST requests

---

## Attacker User Agent

The attacker used the following user agent string:
```
Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/112.0.5615.50 Safari/537.36
```


This user agent was consistent across all brute force attempts and post-exploitation activities from IP address 146.241.73.240.

---

## Conclusion

The investigation confirms a successful **Brute Force attack** on 26/Apr/2023 at 21:44:03 from IP address **146.241.73.240**. The attacker conducted 
hundreds of login attempts against the `/login` endpoint over a period of approximately 90 seconds, failing to authenticate as indicated by 200 OK responses 
with 947-byte responses.

The attack was successful when the server returned a **302 Redirect** response instead of the 200 OK response seen with failed attempts. 
This indicates the attacker correctly guessed valid credentials for the application.

Following successful authentication, the attacker engaged in additional malicious activities including:
- Enumerating all posts (postId=1-10)
- Spamming the comment system with repeated POST requests to `/post/comment`

[br1](images/br1.png)

### Security Weaknesses Identified

1. **No Rate Limiting:** The application allowed hundreds of login attempts without restriction
2. **No Account Lockout:** Failed login attempts did not trigger account lockout
3. **Predictable Responses:** Different HTTP status codes differentiated failed vs successful login attempts
4. **Weak Password Policy:** The application likely allowed weak passwords that were vulnerable to brute force

### Immediate Remediation Actions

1. **Implement Rate Limiting:** Restrict login attempts per IP address and per account
2. **Account Lockout:** Lock accounts after a certain number of failed attempts
3. **Uniform Error Responses:** Return identical responses for both successful and failed login attempts
4. **CAPTCHA Implementation:** Add CAPTCHA after multiple failed attempts
5. **Strong Password Policy:** Enforce strong password requirements
6. **Multi-Factor Authentication:** Implement MFA for added security
7. **Monitoring and Alerting:** Alert on multiple failed login attempts
8. **Session Management:** Invalidate sessions and require re-authentication after suspicious activity
