# Analysis of Web Server Access Log - Insecure Direct Object Reference (IDOR) Attack Investigation

I think this is basically what I do always when I started with the assessments.. I opened the [access log](logs/idor-apache-access-logs.txt) with a text editor and began analyzing the HTTP requests to 
identify the IDOR attack timeline and attacker details. 

---

## Key Findings

### Attack Timeline
**Exploitation Date:** 01/Mar/2022:11:42:32

The IDOR attack began on March 1, 2022 at 11:42:32 when the attacker started enumerating user IDs by accessing the `/dvwa/get_user_info/` endpoint with sequential `id` parameters (1 through 10).

### Attacker Identification
**Attacker IP Address:** 192.168.31.174

The attacker used Firefox 78.0 on Linux (X11), indicating a different attacker from previous attacks observed in the logs.

### Attack Success
**Was the attack successful?** Y

The attack was successful as the attacker was able to access user information for multiple IDs (1-10) without proper authorization controls, retrieving sensitive user data from the application.

### Attack Classification
**Type of Attack:** Insecure Direct Object Reference (IDOR)

The attacker exploited a lack of access controls by directly referencing user IDs in the URL parameter to view information belonging to other users without proper authorization checks.

---

## Attack Pattern Analysis

The attacker followed a systematic approach:

1. **Reconnaissance (11:42:12 - 11:42:16)** - Navigated to the DVWA application and authenticated
2. **Vulnerability Discovery (11:42:32)** - Accessed user info endpoint with ID parameter
3. **User Enumeration (11:42:33 - 11:42:59)** - Systematically enumerated user IDs from 2 to 10
4. **Data Collection** - Retrieved sensitive user information for multiple users

---

## Detailed Attack Timeline

| Time | Activity | Endpoint | Response Size | IP Address |
|------|----------|----------|---------------|------------|
| 11:42:12 | Initial access to DVWA | `/dvwa/` | 302 | 192.168.31.174 |
| 11:42:13 | Login page access | `/dvwa/login.php` | 200 | 192.168.31.174 |
| 11:42:16 | Authentication | `POST /dvwa/login.php` | 302 | 192.168.31.174 |
| 11:42:16 | Dashboard access | `/dvwa/index.php` | 200 | 192.168.31.174 |
| 11:42:32 | **User ID 1** | `/dvwa/get_user_info/?id=1` | 1050 | 192.168.31.174 |
| 11:42:33 | **User ID 2** | `/dvwa/get_user_info/?id=2` | 978 | 192.168.31.174 |
| 11:42:35 | **User ID 3** | `/dvwa/get_user_info/?id=3` | 1242 | 192.168.31.174 |
| 11:42:36 | **User ID 4** | `/dvwa/get_user_info/?id=4` | 1021 | 192.168.31.174 |
| 11:42:37 | **User ID 5** | `/dvwa/get_user_info/?id=5` | 771 | 192.168.31.174 |
| 11:42:45 | **User ID 6** | `/dvwa/get_user_info/?id=6` | 928 | 192.168.31.174 |
| 11:42:46 | **User ID 7** | `/dvwa/get_user_info/?id=7` | 1837 | 192.168.31.174 |
| 11:42:51 | **User ID 8** | `/dvwa/get_user_info/?id=8` | 1423 | 192.168.31.174 |
| 11:42:54 | **User ID 9** | `/dvwa/get_user_info/?id=9` | 999 | 192.168.31.174 |
| 11:42:59 | **User ID 10** | `/dvwa/get_user_info/?id=10` | 1325 | 192.168.31.174 |

---

## Attack Pattern Analysis

### IDOR Vulnerability Exploited

The attacker exploited an IDOR vulnerability where:

1. **Direct Object Reference:** The application used a predictable parameter (`id`) to reference user records
2. **No Access Control:** The application failed to verify that the authenticated user had permission to access the requested user's information
3. **Predictable Pattern:** Sequential user IDs (1, 2, 3, etc.) made enumeration trivial
4. **Sensitive Data Exposure:** User information including names, addresses, and potentially other personal data was exposed

### Manual vs Automated Analysis

**Was the attack carried out by an automated tool?** N

**Evidence supporting manual attack:**

1. **Irregular Timing:** Requests show irregular intervals (1-8 seconds between requests), not consistent automated patterns
2. **Incremental Enumeration:** The attacker manually incremented IDs one by one from 1 to 10
3. **Human-like Behavior:** The attack pattern shows pauses and varied timing typical of manual testing
4. **Response Analysis:** Each request produced different response sizes, suggesting the attacker was manually reviewing results
5. **No Automation Signatures:** No rapid-fire requests or tools like Burp Suite/Automated scanners detected

### Data Points for Manual Attack

| ID | Response Size | Time Between Requests |
|----|---------------|----------------------|
| 1 | 1050 | - |
| 2 | 978 | 1 second |
| 3 | 1242 | 2 seconds |
| 4 | 1021 | 1 second |
| 5 | 771 | 1 second |
| 6 | 928 | 8 seconds |
| 7 | 1837 | 1 second |
| 8 | 1423 | 5 seconds |
| 9 | 999 | 3 seconds |
| 10 | 1325 | 5 seconds |

### Why This is IDOR

1. **Predictable Identifiers:** Sequential numeric IDs are easily guessable
2. **No Authorization Check:** The endpoint likely did not verify user permissions
3. **Data Exposure:** Sensitive user information was returned in responses
4. **Horizontal Privilege Escalation:** The attacker accessed data of other users at the same privilege level

### Evidence of Success

The attack was successful because:

1. All requests returned HTTP 200 OK
2. Different response sizes indicate different data was returned for each ID
3. Sensitive user information was successfully retrieved
4. The application did not block or rate-limit requests
5. No access control errors (403/401) were returned

---

## Conclusion

The investigation confirms a successful **Insecure Direct Object Reference (IDOR) attack** on 01/Mar/2022 conducted from IP address **192.168.31.174**. The exploitation began at **11:42:32** when the attacker 
accessed the `/dvwa/get_user_info/` endpoint with `id=1`. The attacker then systematically enumerated user IDs from 2 to 10, successfully retrieving sensitive user information without proper authorization controls.

The attack was performed **manually** as evidenced by irregular timing patterns, incremental enumeration, and human-like pauses between requests. No automated tool signatures were detected in the attack pattern.

### Immediate Remediation Actions

1. **Implement Access Controls:** Verify user permissions before returning data
2. **Use Indirect References:** Replace direct object references with indirect ones (e.g., UUIDs, tokens)
3. **Session-Based Authorization:** Ensure users can only access their own data
4. **Input Validation:** Validate and sanitize all ID parameters
5. **Rate Limiting:** Implement rate limiting to prevent rapid enumeration
6. **Logging and Monitoring:** Log unauthorized access attempts and monitor for suspicious patterns
7. **Parameter Encryption:** Encrypt or obfuscate ID parameters
8. **Security Testing:** Conduct regular security assessments
