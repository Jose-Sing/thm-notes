# 🛡️ TryHackMe — Cybersecurity 101

## Module: IDS Fundamentals | Study Summary

> **Keywords:** IDS · HIDS · NIDS · Signature-Based · Anomaly-Based · Hybrid IDS · Snort · Detection Rules · PCAP · Intrusion Detection · Zero-Day

---

## 📚 Table of Contents

1. [What is an IDS](#1-what-is-an-ids)
2. [Types of IDS — Deployment Modes](#2-types-of-ids--deployment-modes)
3. [Types of IDS — Detection Modes](#3-types-of-ids--detection-modes)
4. [IDS Example: Snort](#4-ids-example-snort)
5. [Snort Modes](#5-snort-modes)
6. [Snort Usage — Files & Configuration](#6-snort-usage--files--configuration)
7. [Snort Rule Format](#7-snort-rule-format)
8. [Snort Rule Creation & Testing](#8-snort-rule-creation--testing)
9. [Running Snort on PCAP Files](#9-running-snort-on-pcap-files)
10. [Quick Reference](#10-quick-reference)

---

## 1. What is an IDS

A **firewall** acts as the gatekeeper — checking traffic at the network boundary. But once a connection passes through (even a legitimate-looking one used by an attacker), the firewall's job is done. **This is where IDS comes in.**

An **Intrusion Detection System (IDS)** is a security solution that **monitors network traffic from inside the network** and detects malicious activities that already passed through the perimeter.

> 💡 **Analogy:** A firewall is the security guard at the building entrance. An IDS is the surveillance camera system inside — it doesn't stop people from entering, but it watches everything they do once inside and raises an alarm if something suspicious is detected.

**Key characteristic:** IDS is **detection-only** — it generates alerts for security administrators but does **not** take action to block or stop the traffic. That is the job of an IPS (Intrusion Prevention System).

---

## 2. Types of IDS — Deployment Modes

![NIDS vs HIDS deployment comparison](https://tryhackme-images.s3.amazonaws.com/user-uploads/6645aa8c024f7893371eb7ac/room-content/6645aa8c024f7893371eb7ac-1723026309300.png)

| Type | Scope | Strengths | Weaknesses |
|---|---|---|---|
| **HIDS** (Host-based IDS) | Installed on individual hosts — monitors only that specific host | Detailed visibility of host-level activities | Resource-intensive, difficult to manage at scale in large networks |
| **NIDS** (Network-based IDS) | Deployed at the network level — monitors traffic across all hosts | Centralized view of the entire network | Less granular visibility per individual host |

---

## 3. Types of IDS — Detection Modes

| Mode | How it works | Detects zero-days? | Key weakness |
|---|---|---|---|
| **Signature-Based** | Matches traffic against a database of known attack patterns (signatures). The stronger the database, the better the detection. | ❌ No — only detects previously known attacks with existing signatures | Cannot detect new/unknown attacks |
| **Anomaly-Based** | Learns the normal behavior (baseline) of the network, then alerts on any deviation from it. | ✅ Yes — detects unknown threats through behavioral deviation | High false positive rate — legitimate programs with unusual behavior get flagged. Can be reduced by fine-tuning (manually defining normal behavior). |
| **Hybrid IDS** | Combines both signature-based and anomaly-based detection to leverage the strengths of each. | ✅ Yes — uses signatures for known threats, anomaly detection for new ones | More complex to configure and maintain |

> 💡 **Zero-day attack:** An attack exploiting a vulnerability that has never been seen before — no patch exists and no signature exists in any IDS database. Only anomaly-based and hybrid IDS can detect these.

---

## 4. IDS Example: Snort

**Snort** is one of the most widely used **open-source IDS solutions**, developed in 1998. It uses both signature-based and anomaly-based detection through its rule files.

Key characteristics:
- Comes with **built-in rule files** containing known attack patterns
- Supports **custom rule creation** for specific detection needs
- Built-in rules can be **disabled** if they don't apply to your environment
- Can be used for real-time monitoring OR forensic analysis of historical traffic

---

## 5. Snort Modes

![Snort packet sniffer, packet logging, and NIDS mode comparison](https://tryhackme-images.s3.amazonaws.com/user-uploads/6645aa8c024f7893371eb7ac/room-content/6645aa8c024f7893371eb7ac-1722881168080.png)

| Mode | Description | Use Case |
|---|---|---|
| **Packet Sniffer** | Reads and displays network packets without performing analysis. Can output to console or file. | Network troubleshooting — diagnosing performance issues by examining raw traffic flow. |
| **Packet Logging** | Logs network traffic to a PCAP file for later analysis. Captures all traffic and any detections. | Forensic investigations — security team needs historical traffic logs to analyze a past attack's root cause. |
| **NIDS Mode** | Primary IDS mode. Monitors traffic in real-time, applies rule files, and generates alerts when patterns match. | Proactive threat detection — continuous monitoring of network traffic for known attack signatures. |

---

## 6. Snort Usage — Files & Configuration

Snort's main directory is `/etc/snort`. Key files:

```bash
# List Snort's directory contents
ls /etc/snort
```

```
classification.config    reference.config       snort.debian.conf
community-sid-msg.map    rules/                 threshold.conf
gen-msg.map              snort.conf             unicode.map
```

| File/Folder | Purpose |
|---|---|
| `snort.conf` | Main configuration file — define network range, enable/disable rule files, and configure settings |
| `rules/` | Directory containing all rule files (built-in and custom) |
| `rules/local.rules` | The file where custom rules are written |

---

## 7. Snort Rule Format

Every Snort rule follows a strict syntax. Here is the anatomy of a complete rule:

![Snort rule format anatomy diagram](https://tryhackme-images.s3.amazonaws.com/user-uploads/6645aa8c024f7893371eb7ac/room-content/6645aa8c024f7893371eb7ac-1725532438800.png)

**Example rule — detect any ICMP (ping) traffic:**
```
alert icmp any any -> $HOME_NET any (msg:"Ping Detected"; sid:10001; rev:1;)
```

### Rule Components

| Component | Example Value | Description |
|---|---|---|
| **Action** | `alert` | What to do when the rule triggers. `alert` generates an alert and logs the packet. |
| **Protocol** | `icmp` | The protocol to match: `tcp`, `udp`, `icmp`, `ip` |
| **Source IP** | `any` | IP address originating the traffic. `any` matches all IPs. |
| **Source Port** | `any` | Port originating the traffic. `any` matches all ports. |
| `->` | | Direction arrow — traffic flows from source to destination |
| **Destination IP** | `$HOME_NET` | Destination IP. `$HOME_NET` is a variable defined in `snort.conf` representing your network range. |
| **Destination Port** | `any` | Destination port. `any` matches all ports. |
| **msg** | `"Ping Detected"` | Message displayed in the alert when the rule triggers. |
| **sid** | `10001` | Signature ID — unique identifier for this rule. Must be unique across all rules. |
| **rev** | `1` | Revision number — incremented each time the rule is modified. Helps track changes. |

### Rule Syntax Template

```
<action> <protocol> <src_IP> <src_port> -> <dst_IP> <dst_port> (msg:"<message>"; sid:<id>; rev:<num>;)
```

---

## 8. Snort Rule Creation & Testing

### Step 1 — Write the custom rule

Open the custom rules file:

```bash
sudo nano /etc/snort/rules/local.rules
```

Add the rule (example — detect pings to the loopback address):

```
alert icmp any any -> 127.0.0.1 any (msg:"Loopback Ping Detected"; sid:10003; rev:1;)
```

Save: `Ctrl+X` → `Y` → `Enter`

### Step 2 — Run Snort in NIDS mode

```bash
sudo snort -q -l /var/log/snort -i lo -A console -c /etc/snort/snort.conf
```

| Flag | Meaning |
|---|---|
| `-q` | Quiet mode — suppresses startup banner |
| `-l /var/log/snort` | Log output directory |
| `-i lo` | Network interface to monitor (`lo` = loopback) |
| `-A console` | Output alerts directly to the console |
| `-c /etc/snort/snort.conf` | Path to Snort's configuration file |

### Step 3 — Trigger the rule

```bash
ping 127.0.0.1
```

### Step 4 — Observe the alert output

```
07/24-10:46:52.401504  [**] [1:1000001:1] Loopback Ping Detected [**] [Priority: 0] {ICMP} 127.0.0.1 -> 127.0.0.1
07/24-10:46:53.406552  [**] [1:1000001:1] Loopback Ping Detected [**] [Priority: 0] {ICMP} 127.0.0.1 -> 127.0.0.1
```

Each line is one ICMP packet — the rule is working.

---

## 9. Running Snort on PCAP Files

Snort can analyze **historical traffic** stored in PCAP files — useful for forensic investigations of past incidents.

![Snort analyzing PCAP files for forensic investigation](https://tryhackme-images.s3.amazonaws.com/user-uploads/6645aa8c024f7893371eb7ac/room-content/6645aa8c024f7893371eb7ac-1723014181865.png)

```bash
sudo snort -q -l /var/log/snort -r Task.pcap -A console -c /etc/snort/snort.conf
```

| Flag | Meaning |
|---|---|
| `-r Task.pcap` | Read from a PCAP file instead of a live interface |

> 💡 **Pro tips for PCAP analysis:**
> - Pipe console output through `grep` to quickly extract specific `sid:` or `msg:` values
> - Apply a BPF (Berkeley Packet Filter) to reduce noise and focus on relevant traffic: e.g. `tcp port 22 or icmp`

---

## 10. Quick Reference

### IDS Decision Tree

```
Need to monitor a single host in detail?
    → HIDS

Need centralized view of the whole network?
    → NIDS

Only known threats to detect (fast, low overhead)?
    → Signature-Based IDS

Need to catch zero-days and novel attacks?
    → Anomaly-Based or Hybrid IDS
```

### Snort Command Reference

```bash
# Run Snort in NIDS mode on a live interface
sudo snort -q -l /var/log/snort -i <interface> -A console -c /etc/snort/snort.conf

# Run Snort on a PCAP file (forensic analysis)
sudo snort -q -l /var/log/snort -r <file.pcap> -A console -c /etc/snort/snort.conf

# Edit custom rules
sudo nano /etc/snort/rules/local.rules
```

### Snort Rule Quick Template

```
alert <protocol> <src_ip> <src_port> -> <dst_ip> <dst_port> (msg:"<description>"; sid:<unique_id>; rev:<revision>;)
```

### Key Terminology

| Term | Definition |
|---|---|
| **IDS** | Intrusion Detection System — monitors network/host traffic and generates alerts on suspicious activity. Detection only, no blocking. |
| **IPS** | Intrusion Prevention System — like IDS but actively blocks detected threats. |
| **HIDS** | Host-based IDS — installed per host, monitors that host's activities. |
| **NIDS** | Network-based IDS — monitors all traffic across the network from a central point. |
| **Signature** | A known pattern of a specific attack stored in the IDS database. |
| **Baseline** | The learned "normal" behavior of a network/system — used by anomaly-based IDS for comparison. |
| **Zero-Day Attack** | An attack exploiting an unknown vulnerability with no existing patch or signature. |
| **False Positive** | An alert on benign activity incorrectly identified as malicious. |
| **Snort** | Popular open-source IDS using signature and anomaly-based detection with custom rule support. |
| **sid** | Signature ID — unique numeric identifier for each Snort rule. |
| **rev** | Revision number — tracks changes to a Snort rule over time. |
| **PCAP** | Packet Capture — standard file format for storing captured network traffic. Used in Snort's logging and forensic analysis modes. |
| **$HOME_NET** | Snort variable defined in `snort.conf` representing the monitored network's IP range. |

---

## 📝 My Notes & Questions

> *[ Write your questions, insights, and connections to other concepts here ]*

---

*TryHackMe — Cybersecurity 101 | IDS Fundamentals*
