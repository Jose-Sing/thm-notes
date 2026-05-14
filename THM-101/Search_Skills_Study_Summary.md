# 🛡️ TryHackMe — Cybersecurity 101

## Module: Search Skills | Study Summary

> **Keywords:** OSINT · Google Dorking · Shodan · Censys · VirusTotal · CVE · Exploit Database · Man Pages · Technical Documentation

---

## 📚 Table of Contents

1. [Search Engines & Advanced Operators](#1-search-engines--advanced-operators)
2. [Specialized Search Engines](#2-specialized-search-engines)
3. [Vulnerabilities & Exploits](#3-vulnerabilities--exploits)
4. [Technical Documentation](#4-technical-documentation)
5. [Social Media in Cybersecurity](#5-social-media-in-cybersecurity)
6. [Quick Reference](#6-quick-reference)

---

## 1. Search Engines & Advanced Operators

Standard search engines support advanced operators that allow much more precise and targeted searches — a critical skill in reconnaissance and OSINT.

### Google Advanced Operators

| Operator | Purpose | Example |
|---|---|---|
| `"exact phrase"` | Find pages containing the exact word or phrase | `"passive reconnaissance"` |
| `site:` | Limit results to a specific domain | `site:tryhackme.com success stories` |
| `-` | Exclude results containing a specific word | `pyramids -tourism` |
| `filetype:` | Search for specific file types (PDF, DOC, XLS, PPT, etc.) | `filetype:ppt cyber security` |

> 🔗 More advanced operators across all major search engines: [Advanced Search Operators List](https://github.com/cipher387/Advanced-search-operators-list)

### Other Search Engines with Advanced Search Support

- [Google Advanced Search](https://www.google.com/advanced_search)
- [Bing Advanced Search](https://support.microsoft.com/en-us/topic/advanced-search-options-b92e25f1-0085-4271-bdf9-14aaea720930)
- [DuckDuckGo Search Syntax](https://duckduckgo.com/duckduckgo-help-pages/results/syntax/)

---

## 2. Specialized Search Engines

Beyond standard search engines, specialized tools allow searching for very specific types of data relevant to cybersecurity.

### Shodan

[Shodan](https://www.shodan.io/) is a **search engine for Internet-connected devices**. It indexes servers, networking equipment, industrial control systems (ICS), and IoT devices. Useful for finding exposed services, outdated software versions, and misconfigured devices globally.

**Example use:** Search `apache 2.4.1` to find all servers still running that specific version, including their geographic distribution.

> 🔗 [Shodan Search Query Examples](https://www.shodan.io/search/examples)
> 🔗 [Shodan Trends](https://trends.shodan.io/) *(historical data — requires subscription)*

### Censys

[Censys](https://search.censys.io/) is similar to Shodan but focuses on **Internet-connected hosts, websites, certificates, and other Internet assets** rather than raw devices. Key use cases:

- Enumerating domains in use
- Auditing open ports and services
- Discovering rogue or forgotten assets within a network

> 🔗 [Censys Introductory Use Cases](https://docs.censys.com/docs/ls-introductory-use-cases#/)

### Shodan vs Censys

| | Shodan | Censys |
|---|---|---|
| **Focus** | Internet-connected devices (routers, webcams, ICS, IoT) | Hosts, websites, TLS certificates, Internet assets |
| **Best for** | Finding exposed devices and services | Auditing domains, certificates, network assets |

### VirusTotal

[VirusTotal](https://www.virustotal.com/) is an **online multi-engine virus scanning service**. It allows users to:

- Upload files and scan them against dozens of antivirus engines simultaneously
- Submit URLs for scanning
- Input file hashes to check results of previously analyzed files
- Read community comments for deeper context on flagged files

> 💡 A file flagged by some engines but not others may be a false positive. Community comments often provide more accurate context.

### Have I Been Pwned (HIBP)

[Have I Been Pwned](https://haveibeenpwned.com/) checks whether an **email address has appeared in a known data breach**. 

Key insight: many users reuse passwords across platforms. If one platform is breached, those credentials may work on other services the user has accounts on — making HIBP a critical tool for both attackers (OSINT) and defenders (user awareness).

---

## 3. Vulnerabilities & Exploits

### CVE — Common Vulnerabilities and Exposures

The **CVE program** acts as a standardized dictionary of known vulnerabilities. Each vulnerability receives a unique identifier in the format:

```
CVE-YEAR-NUMBER
Example: CVE-2024-29988
```

This standardization ensures that security researchers, vendors, and IT professionals are all referring to the same vulnerability regardless of where they encounter it.

- **Maintained by:** The MITRE Corporation
- **Searchable at:** [cve.org](https://www.cve.org/) and [nvd.nist.gov](https://nvd.nist.gov/) (National Vulnerability Database)

> 💡 **Example:** CVE-2014-0160 is the famous **Heartbleed** vulnerability in OpenSSL.

### Exploit Database

The [Exploit Database](https://www.exploit-db.com/) is a public archive of exploit code submitted by various authors. Some exploits are tested and marked as **verified**.

> ⚠️ Exploiting vulnerable systems without explicit written permission is illegal. The Exploit Database is a legitimate resource for authorized penetration testing and security research only.

### GitHub

[GitHub](https://github.com/) frequently hosts:
- Tools related to specific CVEs
- **Proof-of-Concept (PoC)** code demonstrating vulnerabilities
- Full exploit implementations

Searching a CVE ID directly on GitHub (e.g. `CVE-2014-0160`) often returns multiple relevant repositories.

### Vulnerability Research Workflow

```
Identify vulnerability name or CVE ID
          ↓
Search CVE Program / NVD for technical details
          ↓
Search Exploit Database for working exploit code
          ↓
Search GitHub for PoC code and additional tools
```

---

## 4. Technical Documentation

One of the most important research skills in cybersecurity is knowing how to find and read **official documentation**. It is always the most accurate and up-to-date source of information.

### Linux Manual Pages (man pages)

Every command, system call, library function, and configuration file on Linux/Unix systems has a corresponding man page. Access them directly from the terminal:

```bash
man ip          # manual page for the ip command
man ssh         # manual page for ssh
man nmap        # manual page for nmap
```

Press `q` to quit the man page viewer.

> 💡 If you prefer reading in a browser, search `man ip` in any search engine — the [linux.die.net](https://linux.die.net/man/8/ip) version is typically at the top.

### Microsoft Windows Documentation

Microsoft maintains an official technical documentation portal for all its products and commands:

> 🔗 [Microsoft Learn — Technical Documentation](https://learn.microsoft.com/)

Useful for looking up Windows CLI commands (e.g. `ipconfig`, `netstat`), PowerShell cmdlets, Active Directory configuration, and more.

### Product Documentation

Every major tool and technology used in cybersecurity has official documentation. Always prefer official docs over third-party tutorials:

| Product | Documentation |
|---|---|
| Snort (IDS/IPS) | [snort.org/documents](https://www.snort.org/documents) |
| Apache HTTP Server | [httpd.apache.org/docs](https://httpd.apache.org/docs/) |
| PHP | [php.net/manual](https://www.php.net/manual/en/index.php) |
| Node.js | [nodejs.org/docs](https://nodejs.org/docs/latest/api/) |

---

## 5. Social Media in Cybersecurity

Social media platforms are relevant to cybersecurity from two distinct angles:

### OSINT & Threat Intelligence

Social media is a rich source of information for both attackers and defenders:

- **Attackers** can extract answers to security questions (childhood school, pet names, hometown) directly from public profiles — enabling password reset attacks and account takeovers.
- **Defenders** should audit what employees share publicly and train users on the risks of oversharing.

### Staying Current

Following the right channels on social media and dedicated news outlets is one of the most effective ways to stay updated on:

- New vulnerabilities and CVEs
- Emerging attack techniques
- New defensive tools and technologies
- Industry news and trends

> 💡 **Operational tip:** When exploring unfamiliar platforms for research purposes, use a temporary email address to avoid linking your real identity. Terminate the account when done.

---

## 6. Quick Reference

### Tool Selection Guide

| Goal | Tool |
|---|---|
| Find exact phrases or file types online | Google advanced operators (`"phrase"`, `filetype:`, `site:`) |
| Find exposed/vulnerable Internet devices | [Shodan](https://www.shodan.io/) |
| Audit domains, certificates, network assets | [Censys](https://search.censys.io/) |
| Scan a suspicious file or URL | [VirusTotal](https://www.virustotal.com/) |
| Check if an email was in a data breach | [Have I Been Pwned](https://haveibeenpwned.com/) |
| Look up a known vulnerability | [CVE Program](https://www.cve.org/) / [NVD](https://nvd.nist.gov/) |
| Find exploit code for a CVE | [Exploit Database](https://www.exploit-db.com/) / [GitHub](https://github.com/) |
| Look up a Linux command | `man <command>` in terminal or [linux.die.net](https://linux.die.net/) |
| Look up a Windows command | [Microsoft Learn](https://learn.microsoft.com/) |

### Key Terminology

| Term | Definition |
|---|---|
| **OSINT** | Open Source Intelligence — gathering information from publicly available sources. |
| **Google Dorking** | Using advanced Google search operators to find specific or sensitive information. |
| **CVE** | Common Vulnerabilities and Exposures — standardized IDs for known security vulnerabilities. |
| **NVD** | National Vulnerability Database — NIST's repository of CVE details, scores, and references. |
| **PoC** | Proof of Concept — code that demonstrates a vulnerability is real and exploitable. |
| **Man page** | Manual page — built-in Linux/Unix documentation for commands, system calls, and config files. |
| **HIBP** | Have I Been Pwned — service that checks if an email appeared in known data breaches. |
| **ICS** | Industrial Control Systems — specialized systems that control industrial processes; frequently targeted and indexed by Shodan. |

---

## 📝 My Notes & Questions

> *[ Write your questions, insights, and connections to other concepts here ]*

---

*TryHackMe — Cybersecurity 101 | Search Skills*
