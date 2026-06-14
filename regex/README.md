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
   - 4.3

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

### 4.3

---
