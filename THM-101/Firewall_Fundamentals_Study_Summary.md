# 🛡️ TryHackMe — Cybersecurity 101

## Module: Firewall Fundamentals | Study Summary

> **Keywords:** Firewall · Stateless · Stateful · Proxy Firewall · NGFW · Firewall Rules · Inbound · Outbound · Windows Defender Firewall · ufw · iptables · Netfilter · OSI Model

---

## 📚 Table of Contents

1. [What is a Firewall](#1-what-is-a-firewall)
2. [Types of Firewalls](#2-types-of-firewalls)
3. [Firewall Rules — Components & Actions](#3-firewall-rules--components--actions)
4. [Directionality of Rules](#4-directionality-of-rules)
5. [Windows Defender Firewall](#5-windows-defender-firewall)
6. [Linux Firewall — Netfilter & ufw](#6-linux-firewall--netfilter--ufw)
7. [Quick Reference](#7-quick-reference)

---

## 1. What is a Firewall

A firewall is a security solution that **filters network traffic** — both incoming and outgoing — based on a defined set of rules. It acts as a barrier between a trusted internal network and untrusted external networks (like the internet), deciding what traffic is allowed to pass and what gets blocked.

Firewalls operate at different **OSI model layers** depending on their type, which determines how deeply they can inspect traffic — from basic packet headers all the way to application-layer content.

---

## 2. Types of Firewalls

### Stateless Firewall

- **OSI Layer:** 3 and 4
- Filters packets based solely on **predetermined rules** — no memory of previous connections
- Every packet is evaluated independently, regardless of whether it belongs to an established legitimate session
- **Fast** due to simplicity, but **limited** — cannot apply complex context-aware policies
- Weakness: if previous packets from a source were denied, the firewall forgets this and treats future packets from the same source as new

### Stateful Firewall

- **OSI Layer:** 3 and 4
- Goes beyond static rules by maintaining a **state table** — a record of all active connections
- Packets are evaluated both against rules AND against their relationship with previous connections
- If a connection is established as legitimate, all subsequent packets for that session are automatically allowed without re-inspection
- If a connection is flagged as malicious, all future packets from that source are denied based on the recorded state
- More secure than stateless — understands the **context** of traffic

### Proxy Firewall (Application-Level Gateway)

- **OSI Layer:** 7
- Acts as an **intermediary** between the internal network and the internet
- **Inspects the content** of packets — not just headers — enabling deep analysis
- Forwards requests on behalf of internal users, masking their real IPs for anonymity
- Supports **content filtering policies** to allow/deny traffic based on actual content
- Can decrypt and inspect **SSL/TLS** packets

### Next-Generation Firewall (NGFW)

- **OSI Layer:** 3 through 7 — the most comprehensive
- Provides **deep packet inspection (DPI)** across all layers
- Includes a built-in **Intrusion Prevention System (IPS)** that blocks malicious activity in real time
- Uses **heuristic analysis** to identify and block attack patterns before they reach the network
- **SSL/TLS decryption** — decrypts encrypted traffic, inspects it, re-encrypts
- Integrates with **threat intelligence feeds** for context-aware decisions

### Comparison Table

| Firewall Type | OSI Layer | Key Characteristics                                                                          |
| ------------- | --------- | -------------------------------------------------------------------------------------------- |
| **Stateless** | 3–4       | Basic filtering, no connection tracking, high speed, limited context                         |
| **Stateful**  | 3–4       | Tracks connections via state table, complex rules, context-aware                             |
| **Proxy**     | 7         | Inspects packet content, content filtering, application control, SSL/TLS inspection          |
| **NGFW**      | 3–7       | Advanced threat protection, IPS, heuristic analysis, SSL/TLS decryption, threat intelligence |

---

## 3. Firewall Rules — Components & Actions

### Rule Components

Every firewall rule is built from the following fields:

| Component               | Description                                                      |
| ----------------------- | ---------------------------------------------------------------- |
| **Source Address**      | IP address of the machine originating the traffic                |
| **Destination Address** | IP address of the machine receiving the traffic                  |
| **Port**                | Port number associated with the traffic                          |
| **Protocol**            | Protocol used (TCP, UDP, ICMP, etc.)                             |
| **Action**              | What to do when traffic matches this rule (Allow, Deny, Forward) |
| **Direction**           | Whether the rule applies to incoming or outgoing traffic         |

### Types of Actions

#### Allow

Permits the matched traffic to pass through.

**Example — Allow all outgoing HTTP traffic:**

| Action | Source         | Destination | Protocol | Port | Direction |
| ------ | -------------- | ----------- | -------- | ---- | --------- |
| Allow  | 192.168.1.0/24 | Any         | TCP      | 80   | Outbound  |

#### Deny

Blocks the matched traffic — it does not pass through.

**Example — Deny all incoming SSH traffic to a critical server:**

| Action | Source | Destination    | Protocol | Port | Direction |
| ------ | ------ | -------------- | -------- | ---- | --------- |
| Deny   | Any    | 192.168.1.0/24 | TCP      | 22   | Inbound   |

#### Forward

Redirects matched traffic to a different network segment. Applies to firewalls that also act as routers/gateways.

**Example — Forward all incoming HTTP traffic to the web server:**

| Action  | Source | Destination | Protocol | Port | Direction |
| ------- | ------ | ----------- | -------- | ---- | --------- |
| Forward | Any    | 192.168.1.8 | TCP      | 80   | Inbound   |

---

## 4. Directionality of Rules

| Direction    | Applies to                                    | Example use case                                                          |
| ------------ | --------------------------------------------- | ------------------------------------------------------------------------- |
| **Inbound**  | Incoming traffic only                         | Allow HTTP (port 80) traffic into the web server                          |
| **Outbound** | Outgoing traffic only                         | Block all outgoing SMTP (port 25) from all devices except the mail server |
| **Forward**  | Traffic being routed between network segments | Forward incoming HTTP (port 80) to the internal web server                |

---

## 5. Windows Defender Firewall

Windows Defender Firewall is Microsoft's **built-in firewall** included in Windows OS. It provides basic allow/deny functionality for applications and supports custom rule creation.

**How to open:**

```
Start → search "Windows Defender Firewall" → Enter
```

### Network Profiles

Windows uses **Network Location Awareness (NLA)** to automatically detect the current network type and apply the corresponding firewall profile:

| Profile                      | When applied                            | Typical configuration                              |
| ---------------------------- | --------------------------------------- | -------------------------------------------------- |
| **Private Networks**         | Home/trusted networks                   | Less restrictive — allows more local traffic       |
| **Guest or Public Networks** | Coffee shops, airports, untrusted Wi-Fi | More restrictive — block most incoming connections |

> 💡 Different firewall settings can be configured per profile. Rules applied in Private mode don't affect Public mode and vice versa.

### Key Options on the Dashboard

- **Allow an app through** — whitelist specific apps for each network profile
- **Turn on/off** — enable or disable the firewall per profile (Microsoft does not recommend disabling)
- **Block all incoming connections** — stricter than turning off, recommended over full disable
- **Restore Defaults** — resets all settings to factory state

### Creating Custom Rules (Advanced Settings)

Custom rules are created via `Advanced Settings` → `Inbound Rules` or `Outbound Rules` → `New Rule`.

**Rule Wizard Steps:**

1. **Rule Type** → select `Custom`
2. **Program** → select `All programs` or a specific application
3. **Protocol and Ports** → select TCP/UDP and specify port numbers (comma-separated, no spaces)
4. **Scope** → define specific source/destination IPs or leave as Any
5. **Action** → Allow / Block the connection
6. **Profile** → select which network profiles the rule applies to
7. **Name** → give the rule a descriptive name

**Example — Block all outgoing HTTP and HTTPS traffic:**

| Action | Protocol | Remote Ports | Direction | Profile      |
| ------ | -------- | ------------ | --------- | ------------ |
| Block  | TCP      | 80, 443      | Outbound  | All profiles |

> ⚠️ After applying this rule, no websites will be reachable since all outgoing traffic on ports 80 and 443 is blocked.

---

## 6. Linux Firewall — Netfilter & ufw

### Netfilter

**Netfilter** is the **core firewall framework built into the Linux kernel**. It provides the fundamental capabilities that all Linux firewall tools build upon:

- Packet filtering
- NAT (Network Address Translation)
- Connection tracking

Firewall utilities that use Netfilter:

| Utility       | Description                                                                            |
| ------------- | -------------------------------------------------------------------------------------- |
| **iptables**  | Most widely used. Powerful but complex syntax. Foundation of Linux firewalling.        |
| **nftables**  | Successor to iptables. Enhanced filtering and NAT. Also Netfilter-based.               |
| **firewalld** | Uses Netfilter with predefined rule sets and network zone configurations.              |
| **ufw**       | Beginner-friendly frontend for iptables. Simplifies rule creation with plain commands. |

### ufw — Uncomplicated Firewall

`ufw` wraps `iptables` with a simpler command interface. Rules defined in ufw are translated into iptables rules automatically.

**Essential ufw Commands:**

```bash
# Check firewall status
sudo ufw status

# Enable the firewall
sudo ufw enable

# Disable the firewall
sudo ufw disable

# Set default policy — allow all outgoing traffic
sudo ufw default allow outgoing

# Set default policy — deny all incoming traffic
sudo ufw default deny incoming

# Deny incoming SSH (port 22 TCP)
sudo ufw deny 22/tcp

# Allow incoming HTTP (port 80 TCP)
sudo ufw allow 80/tcp

# List all active rules with line numbers
sudo ufw status numbered

# Delete a rule by line number
sudo ufw delete 2
```

**Example output of `sudo ufw status numbered`:**

```
To          Action      From
--          ------      ----
[ 1] 22/tcp  DENY IN    Anywhere
[ 2] 22/tcp  DENY IN    Anywhere (v6)
```

> 💡 ufw automatically creates both IPv4 and IPv6 rules when you define a rule — that's why you see two entries per rule (plain and `v6`).

### Choosing the Right Linux Firewall Utility

| Factor                         | Recommended tool             |
| ------------------------------ | ---------------------------- |
| Beginner / simple rules needed | **ufw**                      |
| Advanced/granular control      | **iptables** or **nftables** |
| Enterprise with zones/services | **firewalld**                |

---

## 7. Quick Reference

### Firewall Types by Use Case

```
Need basic fast filtering?                  → Stateless Firewall
Need connection-aware filtering?            → Stateful Firewall
Need content inspection + anonymity?        → Proxy Firewall
Need everything + IPS + threat intel?       → NGFW
```

### Common Ports to Know for Firewall Rules

| Port | Protocol | Service                      |
| ---- | -------- | ---------------------------- |
| 22   | TCP      | SSH                          |
| 25   | TCP      | SMTP (email sending)         |
| 53   | UDP/TCP  | DNS                          |
| 80   | TCP      | HTTP                         |
| 443  | TCP      | HTTPS                        |
| 3389 | TCP      | RDP (Windows Remote Desktop) |

### ufw Cheatsheet

```bash
sudo ufw status                    # check status
sudo ufw enable / disable          # turn on/off
sudo ufw default allow outgoing    # default: allow all outgoing
sudo ufw default deny incoming     # default: deny all incoming
sudo ufw allow 80/tcp              # allow incoming HTTP
sudo ufw deny 22/tcp               # deny incoming SSH
sudo ufw status numbered           # list rules with numbers
sudo ufw delete <number>           # delete rule by number
```

### Key Terminology

| Term                   | Definition                                                                                                          |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------- |
| **Firewall**           | Security solution that filters network traffic based on defined rules.                                              |
| **Stateless Firewall** | Filters packets independently using rules — no memory of previous connections.                                      |
| **Stateful Firewall**  | Tracks connection state in a state table — makes decisions based on connection history.                             |
| **State Table**        | A record maintained by stateful firewalls of all active and recent network connections.                             |
| **Proxy Firewall**     | Intermediary firewall that inspects packet content at layer 7 and masks internal IPs.                               |
| **NGFW**               | Next-Generation Firewall — deepest inspection across all layers, IPS, heuristics, threat intel.                     |
| **DPI**                | Deep Packet Inspection — examining the full content of packets, not just headers.                                   |
| **IPS**                | Intrusion Prevention System — actively blocks detected threats in real time.                                        |
| **Heuristic Analysis** | Pattern-based threat detection — identifies attacks based on behavioral signatures.                                 |
| **Netfilter**          | Core Linux kernel framework providing the foundation for all Linux firewall utilities.                              |
| **ufw**                | Uncomplicated Firewall — beginner-friendly frontend for iptables on Linux.                                          |
| **iptables**           | The most widely used Linux firewall utility — powerful but complex syntax.                                          |
| **NLA**                | Network Location Awareness — Windows mechanism that detects network type and applies the matching firewall profile. |
| **Inbound Rule**       | Firewall rule that applies to incoming traffic.                                                                     |
| **Outbound Rule**      | Firewall rule that applies to outgoing traffic.                                                                     |
| **Forward Rule**       | Firewall rule that redirects traffic to a different network segment.                                                |

---

## 📝 My Notes & Questions

> • Script SSH rule extraction at scale (instead of manual scrolling)
>   Use PowerShell: Get-NetFirewallRule -DisplayName '*SSH*' | Get-NetFirewallPortFilter | Format-Table to script accurate extraction at scale.

---

*TryHackMe — Cybersecurity 101 | Firewall Fundamentals*


