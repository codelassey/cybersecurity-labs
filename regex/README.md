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
 
When you're running the same pattern thousands of times (think: scanning a 3-million-event SIEM index), compile the pattern once and reuse it:
 
```python
ip_pattern = re.compile(r"\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}")
 
for log_line in huge_log_file:
    matches = ip_pattern.findall(log_line)
```
 
Compiled patterns are significantly faster in loops because the regex engine doesn't re-parse the pattern string on every call.
 
---

## 6. Building Patterns
