# 🛡️ TryHackMe — Cybersecurity 101
## Module: CAPA | Study Summary
> **Keywords:** CAPA · Static Analysis · MITRE ATT&CK · MAEC · MBC · Namespaces · Capabilities · Malware Analysis · PE · YAML Rules

---

## 📚 Table of Contents

1. [What is CAPA?](#1-what-is-capa)
2. [Running CAPA](#2-running-capa)
3. [Output Block 1 — General File Information](#3-output-block-1--general-file-information)
4. [Output Block 2 — MITRE ATT&CK](#4-output-block-2--mitre-attck)
5. [Output Block 3 — MAEC](#5-output-block-3--maec)
6. [Output Block 4 — Malware Behavior Catalogue (MBC)](#6-output-block-4--malware-behavior-catalogue-mbc)
7. [Output Block 5 — Namespaces](#7-output-block-5--namespaces)
8. [Output Block 6 — Capabilities](#8-output-block-6--capabilities)
9. [CAPA Web Explorer (Very Verbose Mode)](#9-capa-web-explorer-very-verbose-mode)
10. [Quick Reference Cheatsheet](#10-quick-reference-cheatsheet)
11. [Key Terminology](#11-key-terminology)
12. [My Notes & Questions](#12-my-notes--questions)

---

## 1. What is CAPA?

**CAPA** is an open-source tool developed by Mandiant that **automatically identifies capabilities within executable files** using static analysis. It matches binary contents against a library of YAML-based rules and outputs its findings mapped to standard frameworks like MITRE ATT&CK and MBC.

> In other words: You point CAPA at a suspicious `.exe` or `.bin`, and it tells you *what that program can do* — without executing it. It's like reading someone's toolbox and inferring their trade.

**Key use cases:**
- Malware analysis and triage
- Incident response (narrow scope of investigation)
- Threat hunting (map behavior to adversary playbooks)

---

## 2. Running CAPA

CAPA is run from PowerShell pointing it to the target binary.

```powershell
# Navigate to the CAPA directory
cd C:\Users\Administrator\Desktop\capa

# Basic run
capa.exe .\cryptbot.bin

# Verbose output (more detail, slower)
capa.exe .\cryptbot.bin -v

# Very verbose output (most detail, slowest)
capa.exe .\cryptbot.bin -vv

# Export to JSON (for use with CAPA Web Explorer)
capa.exe -j -vv .\cryptbot.bin > cryptbot_vv.json

# Read pre-processed output from a text file
Get-Content .\cryptbot.txt
```

| Option | Description |
|---|---|
| `-h` / `--help` | Show help menu |
| `-v` / `--verbose` | Enable verbose result document |
| `-vv` / `--vverbose` | Enable very verbose result document |
| `-j` | Output in JSON format |

> ⚠️ **Processing note:** CAPA can take several minutes per file. Pre-processing to a `.txt` or `.json` file and reading it later is common practice.

---

## 3. Output Block 1 — General File Information

The first block provides cryptographic hashes and metadata about the analyzed file.

```
┌─────────────┬──────────────────────────────────────────────────────────────┐
│ md5         │ 3b9d26d2e7433749f2c32edb13a2b0a2                             │
│ sha1        │ 969437df8f4ad08542ce8fc9831fc49a7765b7c5                     │
│ sha256      │ ae7bc6b6f6ecb206a7b957e4bb86e0d11845c5b2d9f7a00a482bef63b567 │
│ analysis    │ static                                                        │
│ os          │ windows                                                       │
│ format      │ pe                                                            │
│ arch        │ i386                                                          │
│ path        │ /home/strategos/Room-CAPA/cryptbot.bin                        │
└─────────────┴──────────────────────────────────────────────────────────────┘
```

| Field | Explanation |
|---|---|
| `md5`, `sha1`, `sha256` | Cryptographic hashes — use to search VirusTotal or threat intel databases |
| `analysis` | How CAPA analyzed the file (`static` = no execution) |
| `os` | Target OS for the identified capabilities |
| `format` | File format (`pe` = Windows Portable Executable) |
| `arch` | CPU architecture (`i386` = 32-bit x86) |
| `path` | Where the file was located when analyzed |

---

## 4. Output Block 2 — MITRE ATT&CK

CAPA maps detected capabilities to the **MITRE ATT&CK framework** — a globally recognized knowledge base documenting adversary tactics and techniques.

### Format

| Format | Example | Breakdown |
|---|---|---|
| `Tactic::Technique [TID]` | `Defense Evasion::Obfuscated Files or Information [T1027]` | Tactic + Technique + ID |
| `Tactic::Technique::Sub-Technique [TID.SID]` | `Defense Evasion::Obfuscated Files or Information::Indicator Removal from Tools [T1027.005]` | Tactic + Technique + Sub-Technique + IDs |

### cryptbot.bin ATT&CK Results

```
┌──────────────────────┬───────────────────────────────────────────────────────────────────┐
│ ATT&CK Tactic        │ ATT&CK Technique                                                   │
├──────────────────────┼───────────────────────────────────────────────────────────────────┤
│ DEFENSE EVASION      │ Obfuscated Files or Information [T1027]                            │
│                      │ Obfuscated Files or Information::Indicator Removal from Tools      │
│                      │   [T1027.005]                                                      │
│                      │ Virtualization/Sandbox Evasion::System Checks [T1497.001]          │
│ DISCOVERY            │ File and Directory Discovery [T1083]                               │
│ EXECUTION            │ Command and Scripting Interpreter::PowerShell [T1059.001]          │
│                      │ Shared Modules [T1129]                                             │
│ IMPACT               │ Resource Hijacking [T1496]                                         │
│ PERSISTENCE          │ Scheduled Task/Job::At [T1053.002]                                 │
│                      │ Scheduled Task/Job::Scheduled Task [T1053.005]                     │
└──────────────────────┴───────────────────────────────────────────────────────────────────┘
```

> **Why this matters:** By mapping findings to ATT&CK, analysts can align discovered behaviors with known adversary playbooks, which narrows the scope of incident investigation.

🔗 [MITRE ATT&CK Framework — TryHackMe Room](https://tryhackme.com/r/room/mitre)

---

## 5. Output Block 3 — MAEC

**Malware Attribute Enumeration and Characterization (MAEC)** is a standardized language for encoding and communicating malware details including behaviors, artifacts, and relationships.

```
┌─────────────────────────────┬──────────────────────────┐
│ MAEC Category               │ MAEC Value               │
├─────────────────────────────┼──────────────────────────┤
│ malware-category            │ launcher                 │
└─────────────────────────────┴──────────────────────────┘
```

### Common MAEC Values

| MAEC Value | Description | Typical Behaviors |
|---|---|---|
| **Launcher** | Triggers specific actions similar to malware behavior | Drops payloads, activates persistence, connects to C2, executes functions |
| **Downloader** | Downloads and executes other files | Fetches secondary payloads, pulls updates, retrieves configs |

> **cryptbot.bin** is classified as a **Launcher** — meaning it's designed to initiate malicious actions on the target system, not just serve as a delivery vehicle.

---

## 6. Output Block 4 — Malware Behavior Catalogue (MBC)

**MBC** is a catalogue of malware objectives and behaviors designed to support malware analysis, labeling, similarity analysis, and standardized reporting. It complements MITRE ATT&CK but does not duplicate it.

### MBC Output Format

| Format | Example | Breakdown |
|---|---|---|
| `OBJECTIVE::Behavior::Method [ID]` | `ANTI-STATIC ANALYSIS::Executable Code Obfuscation::Argument Obfuscation [B0032.020]` | Objective + Behavior + Method + ID |
| `OBJECTIVE::Behavior [ID]` | `COMMUNICATION::HTTP Communication [C0002]` | Objective + Behavior + ID |

---

### MBC Objectives (ATT&CK-based)

| Objective | Description |
|---|---|
| **Anti-Behavioral Analysis** | Attempts to avoid detection by hindering sandbox/debugger analysis |
| **Anti-Static Analysis** | Obstructs static analysis to hide malicious intent |
| **Collection** | Gathers data from the targeted machine or network |
| **Command and Control** | Communicates with C2 servers or peers |
| **Credential Access** | Steals account credentials (usernames, passwords) |
| **Defense Evasion** | Bypasses detection and security mechanisms |
| **Discovery** | Collects information about the system/network environment |
| **Execution** | Runs unauthorized commands or code |
| **Exfiltration** | Steals and extracts sensitive data |
| **Impact** | Manipulates, disrupts, or destroys systems/data |
| **Lateral Movement** | Spreads through the network |
| **Persistence** | Remains undetected and operational long-term |
| **Privilege Escalation** | Gains elevated permissions or control |

### MBC Micro-Objectives

These are behaviors that **aren't necessarily malicious** but are commonly abused by malware.

| Micro-Objective | Description |
|---|---|
| **PROCESS** | Creating/terminating processes, checking mutexes, setting thread context |
| **MEMORY** | Allocating, changing, or freeing memory |
| **COMMUNICATION** | DNS, FTP, HTTP, ICMP, SMTP network traffic |
| **DATA** | Checking strings, compressing, encoding/decoding data |
| **FILE SYSTEM** | Creating, reading, writing, deleting files/directories |

---

### MBC Behaviors — Key Reference Table

| Objective | Behavior | ID | Explanation |
|---|---|---|---|
| ANTI-BEHAVIORAL ANALYSIS | Virtual Machine Detection | B0009 | Checks if running in a VM by examining artifacts |
| ANTI-STATIC ANALYSIS | Executable Code Obfuscation | B0032 | Intentionally obscures code to prevent static analysis |
| EXECUTION | Command and Scripting Interpreter | E1059 | Exploits cmd.exe, PowerShell, Bash, Python, etc. |
| DISCOVERY | File and Directory Discovery | E1083 | Enumerates files and directories |
| DEFENSE EVASION | Obfuscated Files or Information | E1027 | Encodes or encrypts files/samples |
| MEMORY | Allocate Memory | C0007 | Uses memory allocation to unpack and execute |
| PROCESS | Create Process | C0017 | Creates processes via WMI or shellcode |
| COMMUNICATION | HTTP Communication | C0002 | Initiates HTTP connections |
| DATA | Check String | C0019 | Inspects strings for specific characteristics |
| DATA | Encode Data | C0026 | Encodes using Base64 or XOR |
| FILE SYSTEM | Create Directory | C0046 | Creates a directory |
| FILE SYSTEM | Delete File | C0047 | Deletes a file |
| FILE SYSTEM | Read File | C0051 | Reads a file |
| FILE SYSTEM | Writes File | C0052 | Writes to a file |

### MBC Methods (Sub-techniques)

| Behavior | Method | ID | Explanation |
|---|---|---|---|
| Executable Code Obfuscation | Argument Obfuscation | B0032.020 | API call arguments calculated at runtime |
| Executable Code Obfuscation | Stack Strings | B0032.017 | Strings built on the stack and discarded after use |
| HTTP Communication | Read Header | C0002.014 | Reads HTTP headers |
| Encode Data | Base64 | C0026.001 | Encodes data using Base64 |
| Encode Data | XOR | C0026.002 | Encodes data using XOR |
| Obfuscated Files or Information | Encoding-Standard Algorithm | E1027.m02 | Uses standard encoding (e.g., Base64) on malware/files |

### Reading a MBC Result — Example

```
┌─────────────────────────────┬──────────────────────────────────┐
│ MBC Objective               │ MBC Behavior                     │
├─────────────────────────────┼──────────────────────────────────┤
│ DATA                        │ Encode Data::Base64 [C0026.001]  │
└─────────────────────────────┴──────────────────────────────────┘
```

| Label | Value | Meaning |
|---|---|---|
| MBC Objective | DATA | Exhibits data-related behaviors (checking, compressing, encoding) |
| MBC Behavior | Encode Data | Can encode data using Base64 or XOR |
| Method | Base64 | Specifically encodes using the Base64 scheme |
| Identifier | C0026.001 | Unique tag for this behavior-method combination |

> **Translation:** This file can encode data using Base64.

---

## 7. Output Block 5 — Namespaces

CAPA uses **namespaces** to group rules with similar purposes into a hierarchical structure.

### Format

```
Capability (Rule Name) :: Top-Level Namespace (TLN) / Namespace
```

Example: `reference anti-VM strings :: anti-analysis / anti-vm / vm-detection`

### Top-Level Namespaces (TLN)

| TLN | Description |
|---|---|
| `anti-analysis` | Rules detecting behaviors that evade analysis (obfuscation, packing, anti-debug) |
| `collection` | Rules for data enumeration and gathering |
| `communication` | Rules for network interaction behaviors |
| `compiler` | Rules identifying build environments or compilers |
| `data-manipulation` | Rules for data alteration (encoding, encryption) |
| `executable` | Rules for attributes in executable files (PE sections, debug info) |
| `host-interaction` | Rules for interactions with the host (file R/W, process creation) |
| `impact` | Rules for potential harmful effects (remote access, data destruction) |
| `internal` | Internal rules — not for analyst reporting |
| `lib` | Building blocks for other rules |
| `linking` | Rules for dynamic loading of external code/libraries |
| `load-code` | Rules for runtime code injection behaviors |
| `malware-family` | Rules linked to specific malware families |
| `nursery` | Staging area for unpolished/draft rules |
| `persistence` | Rules for maintaining access on compromised systems |
| `runtime` | Rules identifying the language/platform of the program |
| `targeting` | Rules for behaviors related to ATM interactions |

### Namespace Hierarchy — Illustration

```
TLN: anti-analysis
├── anti-vm/
│   └── vm-detection/
│       ├── reference-anti-vm-strings.yml
│       ├── reference-anti-vm-strings-targeting-vmware.yml
│       └── reference-anti-vm-strings-targeting-virtualbox.yml
└── obfuscation/
    ├── obfuscated-with-dotfuscator.yml
    └── obfuscated-with-smartassembly.yml
```

---

## 8. Output Block 6 — Capabilities

**Capabilities** are the rule names — each Capability corresponds directly to a YAML rule file. The Capability name equals the rule filename with dashes replacing spaces.

> Example: Capability `reference anti-VM strings` → Rule file `reference-anti-vm-strings.yml`

### Capability → Namespace → Rule Mapping (cryptbot.bin)

| Capability | TLN | Namespace | Rule YAML File |
|---|---|---|---|
| reference anti-VM strings | Anti-Analysis | anti-vm/vm-detection | reference-anti-vm-strings.yml |
| reference anti-VM strings targeting VMWare | Anti-Analysis | anti-vm/vm-detection | reference-anti-vm-strings-targeting-vmware.yml |
| reference anti-VM strings targeting VirtualBox | Anti-Analysis | anti-vm/vm-detection | reference-anti-vm-strings-targeting-virtualbox.yml |
| contain obfuscated stackstrings | Anti-Analysis | obfuscation/string/stackstring | contain-obfuscated-stackstrings.yml |
| reference HTTP User-Agent string | Communication | http/client | reference-http-user-agent-string.yml |
| check HTTP status code | Communication | http | check-http-status-code.yml |
| reference Base64 string | Data Manipulation | encoding/base64 | reference-base64-string.yml |
| encode data using XOR | Data Manipulation | encoding/xor | encode-data-using-xor.yml |
| contain a thread local storage (.tls) section | Executable | pe/section/tls | contain-a-thread-local-storage-tls-section.yml |
| get common file path | Host-Interaction | file-system | get-common-file-path.yml |
| create directory | Host-Interaction | file-system/create | create-directory.yml |
| delete file | Host-Interaction | file-system/delete | delete-file.yml |
| read file on Windows | Host-Interaction | file-system/read | read-file-on-windows.yml |
| write file on Windows | Host-Interaction | file-system/write | write-file-on-windows.yml |
| allocate or change RWX memory | Host-Interaction | process/inject | allocate-or-change-rwx-memory.yml |
| create process on Windows | Host-Interaction | process/create | create-process-on-windows.yml |
| reference cryptocurrency strings | Impact (Nursery) | impact/cryptocurrency | reference-cryptocurrency-strings.yml |
| link function at runtime on Windows | Linking | runtime-linking | link-function-at-runtime-on-windows.yml |
| parse PE header | load-code | load-code/pe | parse-pe-header.yml |
| resolve function by parsing PE exports | load-code | load-code/pe | resolve-function-by-parsing-pe-exports.yml |
| run PowerShell expression | load-code | load-code/powershell | run-powershell-expression.yml |
| schedule task via at | Persistence | scheduled-tasks | schedule-task-via-at.yml |
| schedule task via schtasks | Persistence | scheduled-tasks | schedule-task-via-schtasks.yml |

> ⚠️ **Exception:** Some capabilities live under the `nursery` TLN (unpolished rules) rather than their expected TLN. Example: `reference cryptocurrency strings` should be under `impact` but is in `nursery`.

### Reading a Capability Result — Example

```
┌───────────────────────────────────────────┬───────────────────────────────────────────┐
│ Capability                                │ Namespace                                 │
├───────────────────────────────────────────┼───────────────────────────────────────────┤
│ reference Base64 string                   │ data-manipulation/encoding/base64         │
└───────────────────────────────────────────┴───────────────────────────────────────────┘
```

| Label | Value | Meaning |
|---|---|---|
| Capability | reference base64 string | Malware can encode data using Base64 |
| TLN | data-manipulation | Rules for data transformation behaviors |
| Namespace | encoding/base64 | Rules for Base64 encoding/decoding |
| Rule YAML File | reference-base64-string.yml | The specific rule that was matched |

---

## 9. CAPA Web Explorer (Very Verbose Mode)

The `-vv` flag + JSON export enables use of the **CAPA Web Explorer**, which provides a visual, searchable interface for the results.

### Workflow

```powershell
# Step 1: Export very verbose JSON
capa.exe -j -vv .\cryptbot.bin > cryptbot_vv.json

# Step 2: Open CAPA Web Explorer
# Online: https://mandiant.github.io/capa/explorer/#/
# Offline (in VM): Open capa_web_explorer_offline.html from the Desktop
```

### What -vv Shows

With very verbose mode + Web Explorer, you can see **exactly which strings or patterns within the file matched each rule**.

**Example 1 — anti-VM rule match:**
The rule `reference-anti-vm-strings-targeting-vmware.yml` checks for strings like `/VMWare/i`, `/VMTools/i`, `/vmnet\.sys/i`, etc. CAPA Web Explorer shows exactly which of these strings were found in the analyzed file.

**Example 2 — schtasks rule match:**
The rule `schedule-task-via-schtasks.yml` requires both `/schtasks/i` AND `/\/create /i` to be present. Web Explorer highlights both matched strings.

### Key Features of Web Explorer
- Filter by capability, namespace, or MITRE tactic
- Global search box across all results
- View matched rule YAML content inline
- Much more readable than raw terminal output for 3,000+ line results

---

## 10. Quick Reference Cheatsheet

### Running CAPA

```powershell
capa.exe .\file.bin                       # Standard analysis
capa.exe .\file.bin -v                    # Verbose
capa.exe .\file.bin -vv                   # Very verbose
capa.exe -j -vv .\file.bin > out.json     # JSON export for Web Explorer
Get-Content .\output.txt                  # Read pre-processed results
```

### Output Blocks Summary

| Block | Framework | What it tells you |
|---|---|---|
| General Info | — | Hashes, OS, architecture, analysis type |
| ATT&CK | MITRE ATT&CK | Adversary tactics and techniques used |
| MAEC | MAEC | Malware category (Launcher / Downloader) |
| MBC | MBC | Detailed malware objectives and behaviors |
| Namespaces | CAPA internal | How rules are organized hierarchically |
| Capabilities | CAPA rules | Specific behaviors detected (rule names) |

### Capability Naming Convention

```
Capability Name     →   Rule YAML Filename
"reference Base64 string"  →  "reference-base64-string.yml"
(spaces replaced with dashes)
```

### MBC Identifier Format

```
B = Behavior           e.g. B0009 = Virtual Machine Detection
C = Micro-Behavior     e.g. C0002 = HTTP Communication
E = ATT&CK-linked      e.g. E1059 = Command and Scripting Interpreter
.NNN = Method/Sub-technique   e.g. C0026.001 = Encode Data::Base64
```

---

## 11. Key Terminology

| Term | Definition |
|---|---|
| **CAPA** | Mandiant's tool for automatically identifying capabilities in executable files via static analysis |
| **Static Analysis** | Analyzing a file without executing it |
| **YAML Rule** | A structured file defining conditions CAPA checks against to identify a capability |
| **Capability** | The name of a matched rule — describes something the binary can do |
| **Namespace** | A hierarchical grouping of related rules in CAPA |
| **TLN (Top-Level Namespace)** | The highest level grouping in the CAPA rule hierarchy |
| **MITRE ATT&CK** | Framework documenting adversary tactics, techniques, and procedures (TTPs) |
| **MAEC** | Standardized language for encoding and communicating malware attributes |
| **MBC** | Catalogue of malware objectives and behaviors for analysis and reporting |
| **Objective** | MBC's top-level grouping — based on ATT&CK tactics in malware context |
| **Micro-Objective** | MBC grouping for low-level behaviors not necessarily malicious on their own |
| **Behavior** | A specific documented action a piece of malware can perform |
| **Method** | A sub-technique of a Behavior (more specific implementation detail) |
| **Launcher (MAEC)** | Malware that triggers actions: drops payloads, establishes persistence, connects to C2 |
| **Downloader (MAEC)** | Malware that fetches secondary payloads from the internet |
| **Nursery** | CAPA's staging area for rule files that are not yet fully polished |
| **RWX Memory** | Read-Write-Execute memory — a common indicator of process injection |
| **PE** | Portable Executable — the standard Windows executable file format |

---

## 📝 My Notes & Questions

> *[ Write your questions, insights, and connections to other concepts here ]*

---

*TryHackMe — Cybersecurity 101 | CAPA*
