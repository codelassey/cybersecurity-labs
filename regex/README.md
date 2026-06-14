# Regular Expressions for Cybersecurity
> Within this folder, I compile some regex resources and commands useful to every Cybersecurity Analyst for easy reference. 
> I will also include a jupter notebook (`regex-for-cybersecurity.ipynb`) containing using Regex commands explained here to solve different kind of scenarios.. or I guess you can just call them problems.
---
 
## Table of Contents

1. [What Is a Regular Expression?](#1-what-is-a-regular-expression)





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

## 2.
