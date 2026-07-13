# VirusTotal for SOC Analysts 

## Overview
VirusTotal (VT) aggregates results from multiple AV engines and threat intelligence sources to analyze:
- Files
- Hashes
- IP addresses
- Domains
- URLs

>  **Do not upload sensitive or confidential files.** Uploaded files may be accessible to VirusTotal Premium users.

---

# File Analysis

### Detection
- Shows how many AV engines detect the file.
- **Don't rely on detection count alone.**

### Tags
Quick classification of the file (e.g., Macro, Trojan, Obfuscated, Ransomware).

### Details
Contains:
- SHA256, SHA1, MD5
- File type & size
- Digital signature
- **History (First Seen / Last Analysis)**

**Tip:** If a file has been analyzed before, it likely targeted other organizations and is less likely to be custom malware.

### Relations
Shows related:
- IPs
- Domains
- URLs
- Files

Useful for IOC pivoting and identifying C2 infrastructure.

> **Note:** Relations may be incomplete because malware can behave differently across environments.

### Behavior
Dynamic analysis showing:
- Network connections
- DNS queries
- Registry changes
- File & process activity

Behavior often provides the strongest evidence of maliciousness.

### Community
Contains analyst comments, malware notes, and investigation tips.

---

# URL Analysis

Analyze suspicious URLs to view:
- Detection results
- Reputation
- Related links and redirects

Useful for phishing and malware delivery investigations.

---

# IOC Search

Search for:
- Hashes
- IPs
- Domains
- URLs

Use results to:
- Check reputation
- View historical detections
- Find related files and infrastructure
- Pivot during investigations

---

# Key Takeaways

- **Never trust old URL results.** Always click **Reanalyse** for fresh scans.
- Detection count ≠ maliciousness.
- Review **tags, behavior, history, and relations** before making a conclusion.
- Legitimate installers (e.g., WinRAR) may be flagged as **Adware/PUA**, which doesn't necessarily mean they are malicious.
- Use VirusTotal for **threat intelligence enrichment**, not as the sole source of truth.

---

# SOC Workflow

1. Search or upload IOC.
2. Review **Detection**.
3. Check **Tags & Details**.
4. Examine **History**.
5. Pivot using **Relations**.
6. Analyze **Behavior**.
7. Read **Community** comments.
8. Correlate findings with SIEM, EDR, and other logs.

---

## Best Practices
- Don't rely on detection count alone.
- Reanalyze old URLs.
- Don't upload confidential files.
- Pivot through related IOCs.
- Validate VirusTotal findings with other security tools.