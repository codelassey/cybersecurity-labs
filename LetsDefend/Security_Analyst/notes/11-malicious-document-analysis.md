# Malicious Document Analysis

So basically this course was just about exposing me to tools needed for a malicious document analysis.
A concise guide to analyzing suspicious Microsoft Office documents for malicious macros and embedded 
threats.

## 1. Essential Tools Overview

| Tool | Command Example | Key Use Case |
|------|----------------|--------------|
| `strings` | `strings malicious.doc \| grep -i vba` | Extracts ASCII/Unicode strings; useful for finding `vba` references. |
| `olemeta` | `olemeta malicious.doc` | Reads document metadata. Look for `Normal.dotm` (the `m` indicates **macros**). |
| `oleid` | `oleid malicious.doc` | Provides a risk assessment summary, format info, encryption status, and external relationships (e.g., embedded EXEs). |
| `olevba` | `olevba malicious.doc` | Deeply analyzes VBA macros, highlighting suspicious keywords and indicators. |

## 2. Analysis Workflow

Follow this step-by-step checklist for a quick triage:

1.  **Initial Reconnaissance**
    *   Run `oleid` to get an overall threat level and determine if the file contains VBA macros.
2.  **Metadata Inspection**
    *   Use `olemeta` to check if the template is `Normal.dotm` (indicates macro presence).
    *   Look for suspicious authors or creation dates.
3.  **String Extraction**
    *   Execute `strings malicious.doc | grep -i vba`.
    *   Look for encoded PowerShell, `Shell()`, `CreateObject()`, or URLs.
4.  **Macro Code Analysis**
    *   Run `olevba malicious.doc` to extract the VBA source code.
    *   Review the output for the following indicators (often flagged in the "Suspicious Keywords" section).

## 3. `olevba` Output Structure

When analyzing the output of `olevba`, pay attention to these columns:

| Type | Keyword | Description |
|------|---------|-------------|
| **AutoExec** | `AutoOpen` | Runs automatically when the document is opened. |
| **Suspicious** | `Shell` | Executes system commands. |
| **Suspicious** | `CreateObject` | Instantiates external applications. |
| **Suspicious** | `WScript.Shell` | Used to run scripts or commands. |
| **Suspicious** | `RegEx` | Often used to match patterns for C2 or data exfiltration. |
| **IOC** | `http://` | Potential Command & Control (C2) address. |
| **Hex** | `OLE stream` | Often contains obfuscated or encoded data within the file structure. |

## 4. Common Indicators of Compromise (IoCs)

*   **Auto-Executable Macros:** `AutoOpen`, `Document_Open`, `Workbook_Open`.
*   **Obfuscation:** Heavily encoded strings, use of `Chr()` functions, or variable names that are a single character.
*   **Process Injection/Execution:** `Shell`, `CreateObject("WScript.Shell")`, `MSHTA`, `Regsvr32`.
*   **External Connections:** `WinHttp.WinHttpRequest.5.1`, `XMLHTTP`, or direct IP addresses/URLs in the code.

## 5. Pro Tip

> **Remember:** The `m` in `Normal.dotm` stands for **Macro-Enabled**. If you see this in the metadata, treat the file as high-risk.