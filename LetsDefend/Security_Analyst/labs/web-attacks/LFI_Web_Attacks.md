# Analysis of Web Server Access Log - Local File Inclusion (LFI) Attack Investigation

## Key Findings

### Attack Timeline
**Exploitation Date:** 01/Mar/2022:11:58:35

The LFI attack began on March 1, 2022 at 11:58:35 when the attacker started attempting directory traversal attacks by injecting `../../` into the `page` parameter of the `/dvwa/vulnerabilities/fi/` endpoint.

### Attacker Identification
**Attacker IP Address:** 192.168.31.174

The attacker used Firefox 78.0 on Linux (X11), the same attacker who previously performed the IDOR attack, indicating a persistent 
attacker conducting multiple vulnerability assessments.

### Attack Success
**Was the attack successful?** N

The LFI attack was **unsuccessful**. All directory traversal attempts returned a response size of 50 bytes, which is significantly smaller than legitimate 
file includes (4000+ bytes). This suggests the application properly handled or blocked the path traversal attempts.

### Attack Classification
**Type of Attack:** Local File Inclusion (LFI)

The attacker attempted to include sensitive system files by using path traversal sequences (`../`) to navigate outside the web root directory.

---

## Attack Pattern Analysis

The attacker followed a systematic approach:

1. **Reconnaissance (11:58:02 - 11:58:05)** - Explored the file inclusion vulnerability page by accessing legitimate PHP files
2. **Baseline Testing (11:58:02 - 11:58:05)** - Accessed valid files to understand normal behavior
3. **Path Traversal Attempts (11:58:35 - 11:59:08)** - Attempted to access sensitive files outside the web root
4. **System File Attempts (11:58:54)** - Attempted to access `/etc/passwd` to retrieve user information

---

## Detailed Attack Timeline

| Time | Activity | Endpoint | Response Size | IP Address |
|------|----------|----------|---------------|------------|
| 11:42:32 | IDOR Attack Started | `/dvwa/get_user_info/?id=1` | 1050 | 192.168.31.174 |
| 11:58:02 | Accessed legitimate file | `/fi/?page=include.php` | 4155 | 192.168.31.174 |
| 11:58:03 | Accessed legitimate file | `/fi/?page=file2.php` | 4106 | 192.168.31.174 |
| 11:58:05 | Accessed legitimate file | `/fi/?page=file1.php` | 4074 | 192.168.31.174 |
| 11:58:35 | **Path Traversal:** `../../` | `/fi/?page=../../` | 50 | 192.168.31.174 |
| 11:58:48 | **Path Traversal:** `../../index.php` | `/fi/?page=../../index.php` | 50 | 192.168.31.174 |
| 11:58:54 | **System File:** `/etc/passwd` | `/fi/?page=../../../../../../etc/passwd` | 50 | 192.168.31.174 |
| 11:59:08 | **Path Traversal:** `../../text.php` | `/fi/?page=../../text.php` | 50 | 192.168.31.174 |

---

## Payload Analysis

### Legitimate File Access (Baseline)
The attacker first accessed legitimate files to establish normal application behavior:

| Payload | Response Size | Status |
|---------|---------------|--------|
| `page=include.php` | 4155 bytes | Normal |
| `page=file2.php` | 4106 bytes | Normal |
| `page=file1.php` | 4074 bytes | Normal |

### Path Traversal Attempts (Failed)

| Payload | File Attempted | Response Size | Success? |
|---------|---------------|---------------|----------|
| `page=../../` | Parent directory listing | 50 | N |
| `page=../../index.php` | Root index.php | 50 | N |
| `page=../../../../../../etc/passwd` | System password file | 50 | N |
| `page=../../text.php` | Root text.php | 50 | N |

### Evidence of Failure

1. **Dramatic Response Size Reduction:** Legitimate files returned 4000+ bytes while all malicious attempts returned only 50 bytes
2. **Consistent Error Response:** All traversal attempts returned the exact same size (50 bytes), indicating a common error handling mechanism
3. **No Sensitive Data Exposure:** The `/etc/passwd` attempt returned only 50 bytes instead of the expected file content
4. **Application Protection:** The application likely had proper input validation or security controls that blocked the attempts

### Why the Attack Failed

The application likely had proper security controls:
1. **Input Sanitization:** Removal or filtering of `../` sequences
2. **Path Restriction:** Restriction of file inclusion to a specific directory
3. **Error Handling:** Graceful error handling without exposing sensitive information
4. **Proper Configuration:** PHP configuration with `open_basedir` restrictions
5. **File Inclusion Controls:** Whitelist of allowed files or directories

---

## Attacker Profile

The attacker at **192.168.31.174** conducted an **LFI Attack (11:58:35 - 11:59:08):** Attempted but failed to exploit file inclusion

This suggests the attacker was systematically testing multiple vulnerabilities in the application.

---
## Conclusion

The investigation identifies an **attempted Local File Inclusion (LFI) attack** on 01/Mar/2022 from IP address **192.168.31.174**. The attack began 
at **11:58:35** when the attacker started using path traversal sequences (`../`) to attempt accessing files outside the web root directory.

**The attack was unsuccessful** as evidenced by the dramatic reduction in response size (50 bytes vs 4000+ bytes for legitimate files). The application 
properly handled the traversal attempts, returning error pages without exposing sensitive system files.

The same attacker previously conducted a successful IDOR attack from the same IP address, indicating a methodical approach to vulnerability discovery. 
While the LFI attack failed, the IDOR attack succeeded, highlighting the need for comprehensive security controls across all application endpoints.

### Immediate Remediation Actions

1. **Input Validation:** Strict validation of file paths and parameters
2. **Whitelist Approach:** Restrict file inclusion to a whitelist of allowed files
3. **Path Sanitization:** Remove or escape directory traversal sequences
4. **PHP Configuration:** Set `open_basedir` to restrict file access
5. **Error Handling:** Implement proper error handling to avoid information disclosure
6. **File Permissions:** Apply least privilege principles to file system
7. **Security Testing:** Regular LFI/Path Traversal testing
8. **Web Application Firewall:** Deploy WAF rules to block traversal attempts