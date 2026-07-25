# Detecting Brute Force Attacks - Summary Notes

## What Is a Brute Force Attack
Trial-and-error method to discover passwords, usernames, directories, or encryption keys.
Attack duration depends on complexity of target (simple = fast, complex = years).

---

## Attack Categories

### Online Attacks
Attacker and victim are connected during the attack.

**Passive Online**
No direct contact between attacker and victim.
- Man in the Middle: Intercepting traffic to capture credentials
- Sniffing: Capturing packets on shared network segments (effective with hubs, limited with switches)

**Active Online**
Direct communication with victim service.
- Web/email/RDP/SSH/database login attempts
- Effective for weak passwords
- Risk of account lockouts

### Offline Attacks
Attacker works with previously captured encrypted or hashed data.
No active connection needed.

**Dictionary Attacks**
Testing pre-compiled word lists as passwords.

**Brute Force Attacks**
Trying every possible character combination within a defined range.
Time depends on password complexity and hardware resources.

**Rainbow Table Attacks**
Pre-computed hash comparisons.
Requires significant storage/processing power.

---

## Vulnerable Services
- Web application login pages
- RDP services
- SSH services
- Mail server logins
- LDAP services
- Database services
- Web directories
- DNS servers

---

## Common Attack Tools
- **Aircrack-ng:** WiFi password cracking
- **John the Ripper:** Multi-platform password cracking
- **L0phtCrack:** Windows password cracking
- **Hashcat:** GPU-accelerated hash cracking
- **Ncrack:** Network authentication cracking
- **Hydra:** Parallelized login cracker supporting multiple protocols

---

## Prevention Methods

**Password Best Practices**
- Minimum 8 characters
- Mix of letters, numbers, symbols
- No personally identifiable information
- Unique per account
- Avoid common patterns

**Administrative Controls**
- Account lockout policies after failed attempts
- Progressive delays between attempts
- CAPTCHA/reCAPTCHA implementation
- Strong password enforcement with periodic changes
- Two-factor authentication (2FA)

---

## Detection Examples

### SSH Brute Force Detection

View failed login attempts by user:
```
cat /var/log/auth.log.1 | grep "Failed password" | cut -d " " -f10 | sort | uniq -c | sort
```


View failed attempts by IP:
```
cat /var/log/auth.log.1 | grep "Failed password" | cut -d " " -f12 | sort | uniq -c | sort
```


View successful logins:
```
cat /var/log/auth.log.1 | grep "Accepted password"
```


**Analysis Pattern**
- Multiple failed attempts from same IP
- Followed by successful login
- Indicates successful brute force compromise

### HTTP Login Brute Force Detection
- Monitor web server logs for repeated login attempts
- Compare response packet sizes (failed attempts differ from successful)
- Track timestamps for frequency patterns

### Windows Login Brute Force Detection

**Successful Logins - Event ID 4624**
"An account was successfully logged on"
- Check Logon Type field
  - Type 10 = Remote Desktop/RDP access
- Identifies compromise timeline

**Failed Logins - Event ID 4625**
"An account failed to log on"
- Tracks unsuccessful attempts
- When followed by 4624 = successful brute force

**Detection Workflow**
1. Filter Event ID 4625 to find failed attempts
2. Note username and source IP
3. Check timeframe for clustering (indicates automated tool)
4. Search for Event ID 4624 with same user
5. If exists after failures = successful compromise

---

## Key Detection Indicators
- Multiple failed attempts from single IP
- Repeated attempts on different usernames from same source
- Failed attempts followed by successful login
- Abnormal login times or patterns
- Volume of authentication failures exceeding threshold
- Dictionary-style username patterns

## SIEM Rule Logic
Typical brute force detection rules consider:
- Number of failed attempts within defined timeframe
- Unique source IP tracking
- Target account/user correlation
- Protocol/service context

## Event ID Reference
- **4624:** Successful logon
- **4625:** Failed logon
- **Logon Type 10:** RDP access
- Full reference: ultimatewindowssecurity.com
















