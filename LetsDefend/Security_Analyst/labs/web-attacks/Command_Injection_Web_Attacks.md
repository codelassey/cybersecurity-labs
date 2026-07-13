# Analysis of Web Server Access Log - Command Injection Attack Investigation

I opened the [access log](logs/command-injection-apache-access-logs.txt) with a text editor and began analyzing the HTTP requests to 
identify the command injection attack timeline and attacker details. The log showed a 
complete attack chain against a DVWA instance.

---

## Key Findings

### Attack Timeline
**Exploitation Date:** 01/Mar/2022:09:03:33

The exploitation phase began on March 1, 2022 at 09:03:33 when the attacker first attempted command injection by 
injecting `1.1.1.1;ls` into the `q` parameter of the `/dvwa/vulnerabilities/exec/` endpoint. This was the first attempt to 
execute arbitrary system commands after establishing baseline behavior.

### Attacker Identification
**Attacker IP Address:** 192.168.31.156

The attacker used Firefox 84.0 on Ubuntu Linux, indicating a different attacker from previous attacks observed in the logs.

### Attack Success
**Was the attack successful?** N

The command injection attack was **unsuccessful**. Although the attacker attempted multiple injection techniques, 
the application returned 200 OK status with identical response sizes (4477 bytes) for all requests, indicating that the 
commands were not executed and the application properly handled or escaped the injection attempts.

### Attack Classification
**Type of Attack:** Command Injection (Attempted)

The attacker attempted command injection techniques but was unsuccessful in executing arbitrary system commands due to 
proper input sanitization or security controls.

---

## Attack Pattern Analysis

The attacker followed a methodical approach:

1. **Reconnaissance (09:03:02 - 09:03:06)** - Navigated to the DVWA application and authenticated
2. **Vulnerability Identification (09:03:15)** - Accessed the Command Injection (exec) vulnerability page
3. **Baseline Testing (09:03:21)** - Tested with benign IP address `1.1.1.1` to understand normal behavior
4. **Command Injection Attempts (09:03:33 - 09:05:41)** - Attempted various system commands with different injection techniques
5. **Command Chaining Attempts (09:04:45 - 09:04:56)** - Tested multiple command chaining operators

---

## Detailed Attack Timeline

| Time | Activity | Command Attempted | Response Size | IP Address |
|------|----------|------------------|---------------|------------|
| 09:03:02 | Initial access to DVWA | - | - | 192.168.31.156 |
| 09:03:05 | Login to DVWA | - | - | 192.168.31.156 |
| 09:03:15 | Accessed Command Injection page | - | 4053 | 192.168.31.156 |
| 09:03:21 | **Baseline Test:** `1.1.1.1` | `ping -c 4 1.1.1.1` | 4477 | 192.168.31.156 |
| 09:03:33 | **Attempt:** `1.1.1.1;ls` | `ping -c 4 1.1.1.1;ls` | 4477 | 192.168.31.156 |
| 09:03:50 | **Attempt:** `1.1.1.1;whoami` | `ping -c 4 1.1.1.1;whoami` | 4477 | 192.168.31.156 |
| 09:04:00 | **Attempt:** `1.1.1.1;dir` | `ping -c 4 1.1.1.1;dir` | 4477 | 192.168.31.156 |
| 09:04:45 | **Attempt:** `1.1.1.1&&ls` | `ping -c 4 1.1.1.1&&ls` | 4477 | 192.168.31.156 |
| 09:04:56 | **Attempt:** `1.1.1.1&&dir` | `ping -c 4 1.1.1.1&&dir` | 4477 | 192.168.31.156 |
| 09:05:41 | **Attempt:** `1.1.1.1;pwd` | `ping -c 4 1.1.1.1;pwd` | 4477 | 192.168.31.156 |

---

## Evidence of Failure

### Key Indicators

1. **Consistent Response Size:** All injection attempts returned the exact same response size (4477 bytes) as the baseline test, indicating no additional command output was included

2. **HTTP Status Code:** All requests returned 200 OK, but no command execution output was visible

3. **No Variation:** Unlike successful command injection, there was no variation in response content or size between different command attempts

4. **Command Execution Pattern:** Successful command injection would typically show:
   - Different response sizes for different commands
   - Command output visible in the response
   - Error messages if commands failed
   - System information in the response

### Why the Attack Failed

The application likely had proper security controls in place:
1. Input sanitization/validation
2. Escaping of shell metacharacters (`;`, `&&`, `|`, etc.)
3. Use of `escapeshellcmd()` or `escapeshellarg()` in PHP
4. Restriction of characters allowed in the input field
5. The application may have been configured with proper security settings


---

## Conclusion

The investigation identifies an **attempted Command Injection attack** on 01/Mar/2022 from IP address **192.168.31.156**. 
The attack began at **09:03:33** when the attacker first attempted to execute arbitrary commands using the semicolon injection 
technique. However, **the attack was unsuccessful** as evidenced by consistent response sizes across all injection attempts and 
no command output in the responses.

The attacker attempted multiple injection techniques including semicolon (`;`) command chaining, double ampersand (`&&`) 
conditional execution, and commands targeting both Linux (`ls`, `whoami`, `pwd`) and Windows (`dir`) environments. Despite these 
efforts, the application properly sanitized the input, preventing command execution.

While the attack failed, this investigation highlights the importance of:
- Proper input sanitization
- Escaping shell metacharacters
- Using secure coding practices
- Regular security testing
- Monitoring for attack patterns
