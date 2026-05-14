# 🛡️ TryHackMe — Cybersecurity 101

## Module: Gobuster — The Basics | Study Summary

> **Keywords:** Gobuster · Enumeration · Brute Force · dir · dns · vhost · Wordlist · Subdomain · Virtual Host · HTTP Status Codes

---

## 📚 Table of Contents

1. [What is Gobuster](#1-what-is-gobuster)
2. [Core Concepts: Enumeration vs Brute Force](#2-core-concepts-enumeration-vs-brute-force)
3. [Global Flags](#3-global-flags)
4. [Mode: dir — Directory & File Enumeration](#4-mode-dir--directory--file-enumeration)
5. [Mode: dns — Subdomain Enumeration](#5-mode-dns--subdomain-enumeration)
6. [Mode: vhost — Virtual Host Enumeration](#6-mode-vhost--virtual-host-enumeration)
7. [dns vs vhost — Key Differences](#7-dns-vs-vhost--key-differences)
8. [Practical Lab Notes](#8-practical-lab-notes)
9. [Quick Reference Cheatsheet](#9-quick-reference-cheatsheet)

---

## 1. What is Gobuster

Gobuster is an **open-source offensive security tool written in Golang**. It performs brute-force enumeration of:

- Web directories and files
- DNS subdomains
- Virtual hosts (vhosts)
- Amazon S3 buckets
- Google Cloud Storage buckets

It works by taking a **wordlist**, appending each entry to a target URL or domain, sending requests, and analyzing the responses. It is widely used in penetration testing, bug bounty hunting, and security assessments.

> 📍 **Where it fits in ethical hacking:** Gobuster sits between the **reconnaissance** and **scanning** phases — it maps out the attack surface before exploitation begins.

Gobuster comes **pre-installed on Kali Linux** and the TryHackMe AttackBox.

---

## 2. Core Concepts: Enumeration vs Brute Force

| Concept | Definition |
|---|---|
| **Enumeration** | Listing all available resources, whether accessible or not. Gobuster enumerates directories, subdomains, and virtual hosts. |
| **Brute Force** | Trying every possibility until a match is found — like trying ten keys on a lock until one fits. Gobuster uses wordlists for this. |

Gobuster combines both: it *enumerates* by *brute forcing* with wordlists.

---

## 3. Global Flags

These flags apply across all Gobuster modes (`dir`, `dns`, `vhost`):

| Short | Long | Description |
|---|---|---|
| `-t` | `--threads` | Number of parallel threads. Default is 10. Increase for speed on large wordlists, decrease if the target rate-limits requests. |
| `-w` | `--wordlist` | Path to the wordlist file. Each entry is appended to the target URL or domain. |
| `-o` | `--output` | Writes results to a specified output file. |
| | `--delay` | Time to wait between requests. Useful to evade detection mechanisms that flag high request rates. |
| | `--debug` | Troubleshooting flag — shows extra detail when commands produce unexpected errors. |

---

## 4. Mode: `dir` — Directory & File Enumeration

### Purpose

Enumerates the **directory structure and files** of a web server. Useful for finding hidden paths, admin panels, configuration files, backup files, and more.

> ⚠️ Gobuster does **not enumerate recursively**. If you find an interesting directory, you must run a second scan targeting that specific path.

### Syntax

```bash
gobuster dir -u "http://www.example.thm" -w /path/to/wordlist
```

### dir-Specific Flags

| Short | Long | Description |
|---|---|---|
| `-c` | `--cookies` | Pass a cookie with each request (e.g. a session ID for authenticated scanning). |
| `-x` | `--extensions` | File extensions to search for. E.g. `-x .php,.js` finds PHP and JS files specifically. |
| `-H` | `--headers` | Add a custom HTTP header to every request. |
| `-k` | `--no-tls-validation` | Skip TLS certificate verification. Needed when the target uses a self-signed certificate (common in CTFs and lab environments). |
| `-n` | `--no-status` | Hide status codes from output — keeps results cleaner. |
| `-P` | `--password` | Used with `--username` for authenticated scans. |
| `-U` | `--username` | Used with `--password` for authenticated scans. |
| `-s` | `--status-codes` | Only display responses with specific status codes (e.g. `200`, or range `300-400`). |
| `-b` | `--status-codes-blacklist` | Hide responses with specific status codes. Overrides `-s`. |
| `-r` | `--followredirect` | Follow HTTP redirects (301/302). Without this flag, Gobuster reports the redirect but does not follow it. |

### Examples

```bash
# Basic directory scan
gobuster dir -u "http://www.example.thm" -w /usr/share/wordlists/dirb/small.txt -t 64

# Scan with redirect following
gobuster dir -u "http://www.example.thm" -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -r

# Scan for specific file extensions
gobuster dir -u "http://www.example.thm" -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x .php,.js

# Scan a specific subdirectory
gobuster dir -u "http://www.example.thm/secret" -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 64 -n -x .js
```

### Key Behavior Notes

- The `-u` flag must include the **protocol** (`http://` or `https://`). Omitting it causes the scan to fail.
- Using an **IP address** in `-u` may target a different website than intended if the server uses virtual hosting. Use the **hostname** when possible.
- When using `-b` and `-s` together, `-b` takes priority and overrides `-s`.

---

## 5. Mode: `dns` — Subdomain Enumeration

### Purpose

Brute forces **DNS subdomains** of a target domain. Gobuster queries a DNS server for each constructed subdomain name and reports those that resolve successfully.

> 💡 **Why this matters:** A vulnerability may be patched on the main domain but still present on a subdomain. E.g. `tryhackme.thm` may be patched while `mobile.tryhackme.thm` is not.

### Syntax

```bash
gobuster dns -d example.thm -w /path/to/wordlist
```

### dns-Specific Flags

| Short | Long | Description |
|---|---|---|
| `-d` | `--domain` | The target domain to enumerate subdomains on. Required. |
| `-c` | `--show-cname` | Show CNAME records in results. Cannot be used with `-i`. |
| `-i` | `--show-ips` | Show the IP addresses that discovered subdomains resolve to. |
| `-r` | `--resolver` | Use a custom DNS server for resolving (instead of the system default). |

### Example

```bash
gobuster dns -d example.thm -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt
```

**What Gobuster does:** Takes each word from the wordlist (e.g. `www`, `shop`, `mail`) and queries the DNS for `www.example.thm`, `shop.example.thm`, `mail.example.thm`, etc. It reports those that return a valid DNS response.

---

## 6. Mode: `vhost` — Virtual Host Enumeration

### Purpose

Brute forces **virtual hosts** on a web server. Instead of querying DNS, Gobuster sends HTTP requests directly to the server's IP and changes the `Host:` header in each request to test different hostnames.

### Syntax

```bash
gobuster vhost -u "http://MACHINE_IP" --domain example.thm -w /path/to/wordlist --append-domain
```

### vhost-Specific Flags

| Short | Long | Description |
|---|---|---|
| `-u` | `--url` | The base URL / IP of the target server. |
| | `--domain` | The base domain to append to each wordlist entry (e.g. `example.thm`). |
| | `--append-domain` | Appends the configured domain to each wordlist entry, forming a valid FQDN (e.g. `www.example.thm`). Without this, Gobuster only sends the bare word (e.g. `www`) as the Host header — causing false positives. |
| | `--exclude-length` | Excludes responses by body size (in bytes). Used to filter out false positives that all share a similar response length. Accepts single values or ranges (e.g. `250-320`). |
| `-r` | `--follow-redirect` | Follows HTTP redirects in responses. |
| `-m` | `--method` | HTTP method to use for requests (default: GET). |

### How vhost Mode Works Internally

Gobuster sends an HTTP GET request for each wordlist entry, **changing only the `Host:` header**:

```http
GET / HTTP/1.1
Host: www.example.thm        ← Gobuster changes this on every request
User-Agent: gobuster/3.6
```

The `Host:` header value is built from three parts:
- `www` → from the wordlist entry
- `.example` → second-level domain (set with `--domain`)
- `.thm` → top-level domain (set with `--domain`)

### Full Example

```bash
gobuster vhost -u "http://10.66.181.216" \
  --domain example.thm \
  -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt \
  --append-domain \
  --exclude-length 250-320
```

---

## 7. dns vs vhost — Key Differences

| | `dns` mode | `vhost` mode |
|---|---|---|
| **What it discovers** | Subdomains registered in DNS | Virtual hosts configured on a web server |
| **How it works** | Performs actual DNS lookups | Sends HTTP requests with modified `Host:` header |
| **Requires DNS** | ✅ Yes — subdomain must resolve in DNS | ❌ No — works directly by IP |
| **Flag for target** | `-d` (domain) | `-u` (URL/IP) |
| **Use case** | Finding publicly registered subdomains | Finding hidden virtual hosts not in DNS |
| **Can find internal hosts** | ❌ No | ✅ Yes |

> 💡 **Simple analogy:**
> - **Subdomain (dns mode):** A separate address registered in the phone book (DNS). If it's not in the phone book, dns mode won't find it.
> - **Virtual host (vhost mode):** Multiple apartments in the same building (IP). The building has one address, but inside there are different units. The server decides which website to show based on the `Host:` header — no phone book needed.

---

## 8. Practical Lab Notes

### Lab 1 — dir Mode: Finding a Hidden Directory

**Goal:** Find the hidden directory on `www.offensivetools.thm`.

```bash
gobuster dir -u "http://www.offensivetools.thm" \
  -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -r
```

**Result:** `/secret` directory discovered.

---

### Lab 2 — dir Mode: Finding a .js File with a Flag

**Goal:** Find a `.js` file inside `/secret` and read its contents.

```bash
# Enumerate the /secret directory specifically for .js files
gobuster dir -u "http://www.offensivetools.thm/secret" \
  -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt \
  -t 64 -n -x .js
```

```bash
# Read the file content directly with curl
curl http://www.offensivetools.thm/secret/flag.js
```

**Result:**
```
Flag: THM{ReconWasASuccess}
```

> 💡 `curl` was used here because it fetches the raw HTTP response and prints it to the terminal — identical to what a browser does internally, but without rendering. Works perfectly for text-based files (`.js`, `.txt`, `.html`, `.json`).

---

### Lab 3 — vhost Mode: Finding Virtual Hosts

**Goal:** Find all vhosts on `offensivetools.thm` that return status 200.

```bash
gobuster vhost -u "http://10.66.181.216" \
  --domain offensivetools.thm \
  -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt \
  --append-domain \
  --exclude-length 250-350
```

**Results:**

| Found | Status | Size |
|---|---|---|
| `forum.offensivetools.thm` | 200 | 2635 |
| `store.offensivetools.thm` | 200 | 3014 |
| `www.offensivetools.thm` | 200 | 8806 |
| `WWW.offensivetools.thm` | 200 | 8806 |
| `secret.offensivetools.thm` | 200 | 1550 |

**Answer: 4 unique vhosts** — `www` and `WWW` are the same host, just a case variation in the wordlist. Same IP, same size (8806), same content. They count as one.

---

## 9. Quick Reference Cheatsheet

### Base Command Structures

```bash
# dir mode
gobuster dir -u "http://TARGET" -w /path/to/wordlist [flags]

# dns mode
gobuster dns -d TARGET_DOMAIN -w /path/to/wordlist [flags]

# vhost mode
gobuster vhost -u "http://TARGET_IP" --domain TARGET_DOMAIN -w /path/to/wordlist --append-domain [flags]
```

### Common Wordlists

| Wordlist | Best used for |
|---|---|
| `/usr/share/wordlists/dirb/small.txt` | Quick dir scans |
| `/usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt` | Thorough dir scans |
| `/usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt` | DNS and vhost enumeration |

### Key Flags at a Glance

```bash
-t 64                    # increase threads for speed
-r                       # follow redirects (301/302)
-x .php,.js,.txt         # search for specific file extensions (dir only)
-k                       # skip TLS certificate verification
-n                       # hide status codes from output
-o results.txt           # save output to file
--delay 200ms            # add delay between requests (stealth)
--exclude-length 250-320 # filter false positives by response body size (vhost)
--append-domain          # build full FQDN from wordlist + domain (vhost)
```

### HTTP Status Codes in Context

| Code | Meaning | Gobuster implication |
|---|---|---|
| `200` | OK | Resource exists and is accessible — high value find |
| `301/302` | Redirect | Resource exists but points elsewhere — use `-r` to follow |
| `403` | Forbidden | Resource exists but access is denied — still worth noting |
| `404` | Not Found | Resource does not exist |

### Terminology

| Term | Definition |
|---|---|
| **Enumeration** | Listing all available resources whether accessible or not. |
| **Brute force** | Trying every wordlist entry until a match is found. |
| **Subdomain** | A hostname registered in DNS under a parent domain (e.g. `shop.example.thm`). |
| **Virtual host (vhost)** | A website hosted on the same server/IP as others, identified by the `Host:` HTTP header. |
| **FQDN** | Fully Qualified Domain Name — the complete hostname including subdomain, domain, and TLD (e.g. `www.example.thm`). |
| **`--append-domain`** | Flag that builds a full FQDN by combining wordlist entry + configured domain. Without it, only the bare word is sent as the Host header, producing false positives. |
| **`--exclude-length`** | Filters out responses by byte size. Used to remove false positives that share a common response length (e.g. a generic "not found" page that always returns 276 bytes). |
| **False positive** | A result that looks like a valid find but is not — e.g. a 404 page with a consistent size that Gobuster reports as "Found". |

---

## 📝 My Notes & Questions

> *[ Write your questions, insights, and connections to other concepts here ]*

---

*TryHackMe — Cybersecurity 101 | Gobuster — The Basics*
