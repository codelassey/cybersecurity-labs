# Regular Expressions for Cybersecurity (Regex)
> Within this folder, I compile some regex resources and commands useful to every Cybersecurity Analyst for easy reference. 
> I will also include a jupter notebook (`regex-cybersecurity-labs.ipynb`) containing using Regex commands explained here to solve different kind of scenarios.. or I guess you can just call them problems.
---
 
## Table of Contents

1. [What Is a Regular Expression?](#1-what-is-a-regular-expression)
2. [Why Regex Matters in Cybersecurity](#2-why-regex-matters-in-cybersecurity)
3. [The `re` Module in Python](#3-the-re-module-in-python)
4. [Core Symbols Reference](#4-core-symbols-reference)
   - 4.1 [Character Type Symbols](#41-character-type-symbols)
   - 4.2 [Quantifier Symbols](#42-quantifier-symbols)
   - 4.3 [Anchors](#43-anchors)
   - 4.4 [Character Classes and Groups](#44-character-classes-and-groups)
   - 4.5 [Lookaheads and Lookbehinds](#45-lookaheads-and-lookbehinds)
5. [Key `re` Module Functions](#5-key-re-module-functions)
6. [Building Patterns](#6-building-patterns)
7. [Cybersecurity Pattern Library](#7-cybersecurity-pattern-library)
   - 7.1 [IP Addresses (IPv4 and IPv6)](#71-ip-addresses-ipv4-and-ipv6)
   - 7.2 [Email Addresses](#72-email-addresses)
   - 7.3 [URLs and Domains](#73-urls-and-domains)
   - 7.4 [Hashes (MD5, SHA1, SHA256)](#74-hashes-md5-sha1-sha256)
   - 7.5 [CVE Identifiers](#75-cve-identifiers)
   - 7.6 [Log Entry Parsing](#76-log-entry-parsing)
   - 7.7 [Windows Event IDs](#77-windows-event-ids)
   - 7.8 [SSH Brute Force Detection](#78-ssh-brute-force-detection)
   - 7.9 [User Agent Strings](#79-user-agent-strings)
   - 7.10 [Base64 Encoded Payloads](#710-base64-encoded-payloads)
   - 7.11 [MAC Addresses](#711-mac-addresses)
   - 7.12 [File Paths and Suspicious Extensions](#712-file-paths-and-suspicious-extensions)
   - 7.13 [Registry Keys (Windows)](#713-registry-keys-windows)
   - 7.14 [Credit Card Numbers (PII Detection)](#714-credit-card-numbers-pii-detection)



---

## 1. What Is a Regular Expression?
 
A **regular expression** (regex) is a sequence of characters that defines a search pattern. Think of it like `Ctrl+F` on your keyboard that doesn't just find exact strings but finds patterns of strings. 
Instead of searching for the literal text `"192.168.1.1"`, you can write a pattern that matches *any* IP address formatted as four groups of digits separated by dots.
 
A regex engine reads your pattern and builds a finite automaton that scans text character by character, transitioning between states until it either confirms a match or rejects the substring.
 
```regex
Variable:       "Failed login from 10.0.0.5 at 2024-01-15"

Pattern:        \d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}
Or Pattern      \d+\.\d+\.\d+\.\d+
Match found:    "10.0.0.5"
```
 
Regex is language-agnostic.. thus the same pattern concepts apply in Python, bash (`grep`, `sed`, `awk`), Splunk SPL, Suricata rules, YARA signatures, JavaScript, Go, and virtually every other tool a SOC analyst touches daily.

---

## 2. Why Regex Matters in Cybersecurity
 
Security analysts deal with *massive* amounts of unstructured text (auth logs, firewall logs, PCAP metadata, EDR telemetry, threat intel feeds, SIEM dashboards) and doing this work manually is both slow and error-prone. Regex automates the extraction and classification of indicators.
 
Here's where regex shows up in the SOC:
 
| Use Case | What Regex Does |
|---|---|
| **Log parsing** | Extracts usernames, IPs, timestamps, error codes from raw log lines |
| **IOC extraction** | Pulls hashes, IPs, URLs, email addresses from threat reports |
| **Alert tuning** | Matches patterns in SIEM rules to reduce false positives |
| **IDS/IPS signatures** | Suricata and Snort use PCRE (Perl Compatible Regular Expressions) in rule content matching |
| **YARA rules** | Malware signatures use regex-like string patterns |
| **SOAR playbooks** | N8N, Shuffle, and similar tools use regex in conditional logic to triage alerts |
| **DLP (Data Loss Prevention)** | Detect credit cards, SSNs, API keys in outbound traffic |
| **Incident Response** | Rapidly grep through filesystem artifacts, memory dumps, event logs |
| **Threat hunting** | Search SIEM indexes for anomalous patterns that didn't trigger any alert |
 
---

## 3. The `re` Module in Python
 
Python's built-in `re` module is your gateway to regex. Always import it first:
 
```python
import re
```
 
There's also the `regex` third-party module (`pip install regex`) which adds features like Unicode categories and overlapping matches - useful for advanced work but the standard `re` module covers 95% of SOC tasks.
 
### Raw Strings
 
Always write regex patterns as **raw strings** in Python by prefixing with `r`:
 
```python
# Python interprets \d as an escape sequence.. just bad
pattern = "\d+"
 
# The backslash is passed literally to the regex engine.. I mean which is Good
pattern = r"\d+"
```
 
Without the `r` prefix, Python's string parser may consume backslashes before the regex engine sees them, causing subtle bugs. This is especially nasty with patterns like `\b` (word boundary) which Python's string parser turns into a backspace character. I have experienced such errors recently hence I yeah.. take a note.

In jupyter notebook, I run a regex pattern without the `r` to make you visualize the error. The error you usually see (without the `r` prefix) is close to:

```bash
<>:1: SyntaxWarning: invalid escape sequence '\d'
<>:1: SyntaxWarning: invalid escape sequence '\d'
C:\Users\USER\AppData\Local\Temp\ipykernel_12996\1133821954.py:1: SyntaxWarning: invalid escape sequence '\d'
  ip_pattern = "\d+\.\d+\.\d+\.\d+"
```
 
---

## 4. Core Symbols Reference
 
### 4.1 Character Type Symbols
 
These symbols each match a **single character** of the specified type:
 
| Symbol | Matches | Example | Matches in `"user_1 @host"` |
|---|---|---|---|
| `\w` | Any alphanumeric character or underscore `[a-zA-Z0-9_]` | `\w+` | `user_1`, `host` |
| `\W` | Any NON-alphanumeric character | `\W+` | ` @` |
| `\d` | Any digit `[0-9]` | `\d` | `1` |
| `\D` | Any NON-digit | `\D+` | `user_`, ` @host` |
| `\s` | Any whitespace (space, tab `\t`, newline `\n`, carriage return `\r`) | `\s+` | ` ` (the space) |
| `\S` | Any NON-whitespace | `\S+` | `user_1`, `@host` |
| `.` | Any character **except** newline | `.+` | `user_1 @host` |
| `\.` | A literal period `.` | `192\.168` | `192.168` |
 
**Note:** The dot `.` is dangerous if used carelessly. Why do I say this? Well, suppossing we have `log = ("pass#word,passXword")`, then `re.findall(r"pass.word", log)` would match `['pass#word', 'passXword']`. 
Use `\.` when you mean a literal dot, and be explicit.

###  4.2 Quantifier Symbols
 
Quantifiers attach to the symbol or group **immediately before them** and control how many times that element must appear:
 
| Symbol | Meaning | Example Pattern | Matches |
|---|---|---|---|
| `+` | 1 or more | `\d+` | `1`, `42`, `192` |
| `*` | 0 or more | `\d*` | `` (empty), `1`, `42` |
| `?` | 0 or 1 (makes the preceding element optional) | `https?` | `http`, `https` |
| `{n}` | Exactly n times | `\d{4}` | `2024` |
| `{n,}` | n or more times | `\d{3,}` | `123`, `1234`, `12345` |
| `{n,m}` | Between n and m times (inclusive) | `\d{1,3}` | `1`, `12`, `192` |
 
**Greedy vs Lazy:** By default quantifiers are *greedy* - they match as much as possible. Appending `?` makes them *lazy* (match as little as possible):
 
```python
text = "<script>alert(1)</script>"
re.findall(r"<.+>", text)   # Greedy:  ['<script>alert(1)</script>']
re.findall(r"<.+?>", text)  # Lazy:    ['<script>', '</script>']
```
 
In log parsing, lazy matching often produces the results you actually want.

### 4.3 Anchors
 
Anchors don't match characters, they match **positions** in the string:
 
| Symbol | Position | Example | Use Case |
|---|---|---|---|
| `^` | Start of string  | `^ERROR` | Lines beginning with ERROR |
| `$` | End of string  | `\.exe$` | Strings ending in .exe |

 
```python
log = "rootkit downloaded by root.exe"
log1 = "ERROR.. not successfull"
a = re.findall(r"^ERROR.+", log1)
b = re.findall(r".+\.exe$", log)
a, b

#OUTPUT = (['ERROR.. not successfull'], ['rootkit downloaded by root.exe'])
```

### 4.4 Character Classes and Groups
 
**Character classes** `[...]` match any single character listed inside:
 
```python
# Match any vowel
r"[aeiouAEIOU]"
 
# Match hex digits
r"[0-9a-fA-F]"
 
# Match anything that's NOT a digit (same as \D)
r"[^\d]"
 
# The ^ inside brackets means NOT
r"[^a-z]"  # Any character that is not a lowercase letter
```
 
**Groups** `(...)` serve two purposes:
1. **Capturing** - extract the matched portion
2. **Grouping** - apply quantifiers to multiple characters at once

```python
# Capturing group - parentheses create a group whose match is returned
log = "user=admin action=login"
match = re.search(r"user=(\w+)", log)
print(match.group(1))  # 'admin'

## Above is because user=(\w+)
##                        ^^^
##                    capture this

## - \w+ matches: admin
## - and because it is inside ( ), it becomes group 1.
```
```python
# Non-capturing group - use (?:...) when you just need to group, not capture

r"(?:https?|ftp)://\S+"  # Matches URLs starting with http, https, or ftp
```
 
**Alternation** `|` works like OR:
 
```python

r"ssh|rdp|telnet"     # Matches any of the three protocols
r"\b(GET|POST|PUT|DELETE|PATCH)\b"  # HTTP methods

# \b prevents matching inside larger words such as TARGET

```

### 4.5 Lookaheads and Lookbehinds
 
These are **zero-width assertions** - they look ahead or behind the current position without consuming characters. They're powerful for extracting values when the surrounding context matters.
 
| Syntax | Type | Meaning |
|---|---|---|
| `(?=...)` | Positive lookahead | Match if followed by ... |
| `(?!...)` | Negative lookahead | Match if NOT followed by ... |
| `(?<=...)` | Positive lookbehind | Match if preceded by ... |
| `(?<!...)` | Negative lookbehind | Match if NOT preceded by ... |
 
```python
log = "password=S3cur3P@ss username=admin"
 
# Extract the value AFTER "password=" without including "password=" in the result
a = re.findall(r"(?<=password=)\S+", log)  # ['S3cur3P@ss']
 
# Find digits NOT followed by a period (avoid matching IP octets mid-address)
b = re.findall(r"\d+(?!\.)", "Port 443 IP 10.0.0.1")  # ['443', '1', '1']
 
# Find "admin" only when NOT preceded by "non-"
c = re.findall(r"(?<!non-)admin", "admin nonadmin non-admin")  # ['admin', 'admin']

a, b, c
```

---

## 5. Key `re` Module Functions
 
| Function | Purpose | Returns |
|---|---|---|
| `re.findall(pattern, string)` | Find all non-overlapping matches | List of strings |
| `re.search(pattern, string)` | Find first match anywhere in string | Match object or None |
| `re.match(pattern, string)` | Match only at the **beginning** of string | Match object or None |
| `re.fullmatch(pattern, string)` | Match must cover the entire string | Match object or None |
| `re.finditer(pattern, string)` | Iterator over all matches | Iterator of Match objects |
| `re.sub(pattern, repl, string)` | Replace all matches with `repl` | New string |
| `re.split(pattern, string)` | Split string on pattern | List of strings |
| `re.compile(pattern, flags)` | Pre-compile a pattern for reuse | Pattern object |
 
### Match Objects
 
`re.search()`, `re.match()`, and `re.fullmatch()` return a **Match object** when a match is found, or `None` otherwise. Always check for `None` before calling methods on it:
 
```python
log_line="192.168.56.1"
match = re.search(r"(\d{1,3}\.){3}\d{1,3}", log_line)
if match:
    print(match.group())    # The entire match = 192.168.56.1
    print(match.group(1))   # First capturing group = 56.
    print(match.start())    # Start index in the string = 0
    print(match.end())      # End index in the string = 12
    print(match.span())     # (start, end) tuple = (0, 12)


```
 
### Compiling Patterns
 
When you're running the same pattern thousands of times, compile the pattern once and reuse it:
 
```python
ip_pattern = re.compile(r"\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}")
 
for log_line in huge_log_file:
    matches = ip_pattern.findall(log_line)
```
 
Compiled patterns are significantly faster in loops because the regex engine doesn't re-parse the pattern string on every call.
 
---

## 6. Building Patterns

The key skill in regex is **decomposing** what you want to match into small, describable chunks, then translating each chunk into symbols.
 
### Example: Parsing an Apache Access Log Line
 
```
192.168.1.105 - admin [15/Jan/2024:10:22:33 +0000] "GET /admin/config.php HTTP/1.1" 200 4523
```
 
**Goal:** Extract IP, username, timestamp, HTTP method, path, and status code.
 
**Step 1 - IP address:**
- Four groups of 1–3 digits separated by dots
- Pattern: `\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}`
**Step 2 - Username (between the two dashes):**
- A word character sequence: `\w+`
- Preceded by `- ` and followed by ` [`: use lookbehind/lookahead or just capture with context
**Step 3 - Timestamp in brackets:**
- `\[` literal bracket, then date/time content, then `\]`
- Pattern: `\[([^\]]+)\]`
**Step 4 - HTTP method and path:**
- Pattern: `"(GET|POST|PUT|DELETE|PATCH|HEAD|OPTIONS) (/\S*)`
**Step 5 - Status code:**
- Three digits at a specific position
- Pattern: `" (\d{3}) `
**Combined pattern:**
 
```python
import re
 
log = '192.168.1.105 - admin [15/Jan/2024:10:22:33 +0000] "GET /admin/config.php HTTP/1.1" 200 4523'
 
pattern = r'(\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}) - (\w+) \[([^\]]+)\] "(\w+) (\S+) HTTP/[\d.]+" (\d{3})'
match = re.match(pattern, log)
 
if match:
    ip, user, timestamp, method, path, status = match.groups()
    print(f"IP: {ip}, User: {user}, Timestamp: {timestamp}, Method: {method}, Path: {path}, Status: {status}")
```
 
---

## 7. Cybersecurity Pattern Library
 
### 7.1 IP Addresses (IPv4 and IPv6)
 
**IPv4 - basic (1–3 digits per octet):**
```python
r"\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}"
```
 
**IPv4 - strict (valid range 0–255 per octet):**
This is longer but more accurate - avoids matching `999.999.999.999`:
```python
r"(?:(?:25[0-5]|2[0-4]\d|[01]?\d\d?)\.){3}(?:25[0-5]|2[0-4]\d|[01]?\d\d?)"
```
 
**IPv6 - full form:**
```python
r"(?:[0-9a-fA-F]{1,4}:){7}[0-9a-fA-F]{1,4}"
```
 
**IPv6 - compressed form (handles `::` notation):**
```python
r"(?:[0-9a-fA-F]{0,4}:){2,7}[0-9a-fA-F]{0,4}"
```
 
**Private IP detection** (RFC 1918):
```python
r"(?:10\.\d{1,3}\.\d{1,3}\.\d{1,3}|172\.(?:1[6-9]|2\d|3[01])\.\d{1,3}\.\d{1,3}|192\.168\.\d{1,3}\.\d{1,3})"
```
 
### 7.2 Email Addresses

**Basic:**
```
r"\w+@\w+\.\w+"r"\w+@\w+\.\w+"
```

**Long:**
```python
r"[a-zA-Z0-9._%+\-]+@[a-zA-Z0-9.\-]+\.[a-zA-Z]{2,}"
```
 
**Phishing detection - suspicious TLDs or patterns:**
```python
# Emails with suspicious TLDs or hyphenated domains (common in phishing)
r"[a-zA-Z0-9._%+\-]+@[a-zA-Z0-9\-]+(?:\.[a-zA-Z0-9\-]+)*\.(?:tk|ml|ga|cf|gq|xyz|top|click|download)"
```
 
### 7.3 URLs and Domains
 
**General URL:**
```python
r"https?://(?:www\.)?[a-zA-Z0-9\-\.]+\.[a-zA-Z]{2,}(?:/[^\s]*)?"
```
 
**URL with optional port:**
```python
r"https?://[a-zA-Z0-9\-\.]+(?::\d{1,5})?(?:/[^\s]*)?"
```
 
**Suspicious URL indicators** (common in phishing/malware C2):
```python
# IPs used directly as host (instead of domain)
r"https?://\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}(?::\d+)?(?:/\S*)?"
 
# Encoded characters in URL (obfuscation)
r"https?://\S*%[0-9a-fA-F]{2}\S*"
```
 
**Domain extraction:**
```python
r"(?:^|\s|\()([a-zA-Z0-9](?:[a-zA-Z0-9\-]{0,61}[a-zA-Z0-9])?\.(?:[a-zA-Z]{2,}))"
```
 
### 7.4 Hashes (MD5, SHA1, SHA256)
 
File hashes are key IOCs (Indicators of Compromise) in malware analysis.
 
```python
# MD5 - 32 hex characters
r"\b[0-9a-fA-F]{32}\b"
 
# SHA1 - 40 hex characters
r"\b[0-9a-fA-F]{40}\b"
 
# SHA256 - 64 hex characters
r"\b[0-9a-fA-F]{64}\b"
 
# SHA512 - 128 hex characters
r"\b[0-9a-fA-F]{128}\b"
 
# Any hash (32, 40, 64, or 128 hex chars)
r"\b[0-9a-fA-F]{32}(?:[0-9a-fA-F]{8}(?:[0-9a-fA-F]{16}(?:[0-9a-fA-F]{64})?)?)?\b"
```
 
**Practical use - extract hashes from a VirusTotal report:**
```python
import re
 
report = """
File: malware.exe
MD5:    d41d8cd98f00b204e9800998ecf8427e
SHA1:   da39a3ee5e6b4b0d3255bfef95601890afd80709
SHA256: e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855
"""
 
md5    = re.findall(r"\b[0-9a-fA-F]{32}\b", report) # or md5    = re.findall(r"[\w+]{32}", report)
sha1   = re.findall(r"\b[0-9a-fA-F]{40}\b", report) # or sha1   = re.findall(r"[\w+]{40}", report)
sha256 = re.findall(r"\b[0-9a-fA-F]{64}\b", report) # or sha256 = re.findall(r"[\w+]{64}", report)

print(f"MD5: {md5[0]}")
print(f"SHA1: {sha1[0]}")
print(f"SHA256: {sha256[0]}")

```
 
### 7.5 CVE Identifiers
 
```python
# Standard CVE format: CVE-YEAR-IDNUMBER
r"\bCVE-\d{4}-\d{4,7}\b"
 
# Case-insensitive match (some reports write "cve-2024-1234")
re.findall(r"\bCVE-\d{4}-\d{4,7}\b", text, re.IGNORECASE)
```
 
### 7.6 Log Entry Parsing

**Syslog format:**
```
log = "Jan 15 10:22:33 hostname sshd[12345]: Failed password for root from 192.168.1.5 port 22 ssh2"
```
```python
pattern = r"(\w{3}\s+\d{1,2}\s+\d{2}:\d{2}:\d{2})\s+(\S+)\s+(\S+):\s+(.+)"

showme = re.findall(pattern, log)
print(showme)

# Groups: timestamp, hostname, process, message

# [('Jan 15 10:22:33', 'hostname', 'sshd[12345]', 'Failed password for root from 192.168.1.5 port 22 ssh2')]
```
 
**Auth.log - failed SSH login:**
```python
r"Failed password for (?:invalid user )?(\w+) from (\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}) port (\d+)"

# Groups: username, IP, port
# [('root', '192.168.1.5', '22')]
```
 
**Nginx/Apache access log:**
```python
r'(\d{1,3}(?:\.\d{1,3}){3}) - (\S+) \[([^\]]+)\] "(\w+) (\S+) HTTP/[\d.]+" (\d{3}) (\d+)'
# Groups: IP, user, timestamp, method, path, status, bytes
```
 
**Windows Event Log (text export):**
```python
# Extract EventID
r"EventID:\s*(\d+)"
 
# Extract Account Name from Security events
r"Account Name:\s*(\S+)"
 
# Logon Type extraction
r"Logon Type:\s*(\d+)"
```
 
**Cowrie SSH honeypot log:**
```python
r'(\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}\.\d+Z)\s+\[(\S+)\]\s+(.+)'
```

### 7.7 Windows Event IDs
 
Common Event IDs worth hunting for. These appear in raw log text when exporting from Event Viewer or parsing `.evtx` files:
 
```python
# Critical event IDs for quick reference
critical_event_ids = {
    "4624": "Successful logon",
    "4625": "Failed logon",
    "4648": "Logon with explicit credentials",
    "4672": "Special privileges assigned to new logon",
    "4688": "New process created",
    "4698": "Scheduled task created",
    "4720": "User account created",
    "4732": "User added to local group",
    "4768": "Kerberos TGT requested",
    "4769": "Kerberos service ticket requested",
    "7045": "New service installed",
}
 
# Find all event IDs in a log dump
event_id_pattern = r"(?:EventID|Event ID):\s*(\d{4})"
```

### 7.8 SSH Brute Force Detection
