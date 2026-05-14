# 🛡️ TryHackMe — Cybersecurity 101

## Module: SIEM | Study Summary

> **Keywords:** SIEM · Log Sources · Centralization · Normalization · Parsing · Correlation · Detection Rules · Alerting · Splunk · Syslog · Agent · Forwarder · Host-Centric · Network-Centric

---

## 📚 Table of Contents

1. [The Problem — Logs Everywhere, Answers Nowhere](#1-the-problem--logs-everywhere-answers-nowhere)
2. [What is SIEM & Why it Exists](#2-what-is-siem--why-it-exists)
3. [Core SIEM Features](#3-core-siem-features)
4. [Log Sources & Their Formats](#4-log-sources--their-formats)
5. [Log Ingestion Methods](#5-log-ingestion-methods)
6. [Detection Rules & Alerting](#6-detection-rules--alerting)
7. [Alert Investigation Process](#7-alert-investigation-process)
8. [Quick Reference](#8-quick-reference)

---

## 1. The Problem — Logs Everywhere, Answers Nowhere

Every device in a network continuously generates logs. These **log sources** fall into two categories:

### Host-Centric Log Sources

Events that occur within or directly related to a host (Windows, Linux, servers):

- User accessing a file
- User attempting to authenticate
- Process execution activity
- Process adding/editing/deleting a registry key
- PowerShell execution

### Network-Centric Log Sources

Events generated when hosts communicate with each other or with the internet (firewalls, IDS/IPS, routers):

- SSH connections
- File access via FTP
- Web traffic
- VPN access to company resources
- Network file sharing activity

### Why This Becomes a Problem

Despite the value of logs, analyzing them at scale presents serious challenges:

| Challenge                | Description                                                                                                                                                                                   |
| ------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Numerous Log Sources** | Hundreds of events per second across many devices — scattered and hard to manage.                                                                                                             |
| **No Centralization**    | Logs live on the machines that generate them. Analysts must connect to each device individually via SSH/RDP — extremely inefficient during incidents.                                         |
| **Limited Context**      | Individual logs tell only part of the story. A file access event looks normal in isolation — but correlated with lateral movement logs from another machine, it becomes evidence of a breach. |
| **Limited Analysis**     | The volume of logs makes manual analysis nearly impossible. Analysts inevitably miss critical events.                                                                                         |
| **Format Issues**        | Windows logs don't look like Linux logs. Different formats require different knowledge to parse — multiplied across dozens of log source types.                                               |

---

## 2. What is SIEM & Why it Exists

**SIEM (Security Information and Event Management)** is a security solution that:

1. **Collects** logs from all types of log sources across the network
2. **Standardizes** (normalizes) their format into a consistent structure
3. **Correlates** them to find relationships between events
4. **Detects** malicious activities using detection rules
5. **Alerts** analysts in real time when threats are identified

> 💡 SIEM directly solves every challenge listed above — centralization, normalization, correlation, scale, and format inconsistency — in a single platform.

---

## 3. Core SIEM Features

### Centralized Log Collection

SIEM pulls logs from all sources (endpoints, servers, firewalls, etc.) into one centralized location using lightweight **agents** or **APIs**. Eliminates the need to individually access each device during an investigation.

### Normalization of Logs

Raw logs arrive in different formats and sizes. SIEM processes them in two steps:

| Step              | Definition                                                                             |
| ----------------- | -------------------------------------------------------------------------------------- |
| **Parsing**       | Breaking a raw log down into individual fields (IP, timestamp, event type, user, etc.) |
| **Normalization** | Converting all parsed logs from different sources into one consistent, unified format  |

> 💡 Normalized logs are what make detection rules possible — rules compare field values, so all logs must share the same field structure.

### Correlation of Logs

SIEM links related events across different log sources and finds patterns. The real power of SIEM is in correlation — individually harmless events become suspicious when seen together.

**Example — Data Exfiltration Detection (5-minute window):**

| Event                                                         | Individually looks like... | Correlated, means...           |
| ------------------------------------------------------------- | -------------------------- | ------------------------------ |
| Jose logs in via VPN from an unfamiliar IP                    | Odd but not alarming       | Possible credential compromise |
| Jose accesses documents on a shared drive                     | Normal file access         | Targeting sensitive data       |
| Jose executes a PowerShell script | Routine admin task         | Staging exfiltration           |
| System makes an outbound network connection                   | Normal internet traffic    | Data leaving the network       |

Together → **Potential data exfiltration via compromised VPN credentials.**

### Real-Time Alerting

SIEM continuously evaluates incoming logs against **detection rules**. When rule conditions are met, an alert is triggered and analysts are notified immediately. Rules can be:

- Built-in defaults provided by the SIEM
- Custom rules created by analysts to address specific threat scenarios

### Dashboards and Reporting

Dashboards are the primary interface analysts use for monitoring. They present normalized, correlated data as actionable visual insights. Common dashboard elements include:

- Alert highlights
- System notifications
- Health alerts
- Failed login attempt lists
- Events ingested count
- Rules triggered
- Top domains visited

> 💡 Most SIEM solutions provide default dashboards and allow custom dashboard creation — example: **Splunk**.

### Additional SIEM Capabilities

- Integration with **threat intelligence feeds**
- Extensive **data retention**
- Powerful **search capabilities**

---

## 4. Log Sources & Their Formats

### Windows Machine

Windows logs all events and assigns each a unique **Event ID**. Viewed natively through **Event Viewer**. Forwarded to SIEM via agent/forwarder for centralized monitoring.

### Linux Machine

Linux stores logs in specific directories:

| Path                                    | Content                              |
| --------------------------------------- | ------------------------------------ |
| `/var/log/httpd`                        | HTTP request/response and error logs |
| `/var/log/cron`                         | Cron job execution events            |
| `/var/log/auth.log` / `/var/log/secure` | Authentication-related logs          |
| `/var/log/kern`                         | Kernel-related events                |

**Sample cron log:**

```
May 28 13:04:20 ebr crond[2843]: /usr/sbin/crond 4.4 dillon's cron daemon, started with loglevel notice
Jun 13 07:46:22 ebr crond[3592]: unable to exec /usr/sbin/sendmail: cron output for user root job sys-daily
```

### Web Server (Apache)

All requests and responses to/from the web server are logged. Common locations:

- `/var/log/apache`
- `/var/log/httpd`

**Sample Apache log:**

```
192.168.21.200 - - [21/March/2022:10:17:10 -0300] "GET /cgi-bin/try/ HTTP/1.0" 200 3395 "-" "Mozilla/5.0..."
127.0.0.1 - - [21/March/2022:10:22:04 -0300] "GET / HTTP/1.0" 200 2216 "-" "curl/7.68.0"
```

---

## 5. Log Ingestion Methods

Each SIEM has its own ingestion approach. The four common methods are:

| Method                | How it works                                                                                                                                                   | Best for                                       |
| --------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------- |
| **Agent / Forwarder** | A lightweight tool installed on each endpoint that captures and forwards logs to the SIEM server. Splunk calls this a "forwarder".                             | Continuous real-time collection from endpoints |
| **Syslog**            | A widely used standard protocol for collecting data from web servers, databases, and network devices and sending it to a centralized destination in real time. | Network devices and Linux systems              |
| **Manual Upload**     | Offline log files are uploaded directly into the SIEM (supported by Splunk, ELK, etc.) for one-time or ad-hoc analysis.                                        | Historical analysis, incident retrospectives   |
| **Port Forwarding**   | SIEM listens on a specific port; endpoints are configured to forward logs directly to that port.                                                               | Custom or legacy integrations                  |

---

## 6. Detection Rules & Alerting

Detection rules are the **logical expressions** that power SIEM alerting. They watch for specific field values or combinations of conditions across ingested logs and trigger alerts when those conditions are met.

### How Detection Rules Work

A rule monitors field-value pairs in normalized logs. Because logs are normalized, every log — regardless of source — shares consistent field names, making rule conditions reliable and accurate.

### Detection Rule Examples

**Use Case 1 — Log Clearing Detection:**

Attackers often delete event logs during post-exploitation to remove their tracks. Windows logs this action with Event ID `104`.

```
Rule: IF Log Source = WinEventLog AND EventID = 104
      THEN trigger alert → "Event Log Cleared"
```

**Use Case 2 — Suspicious Command Execution:**

Attackers commonly run `whoami` after exploitation to confirm their privilege level. Windows logs process creation with Event ID `4688`.

```
Rule: IF Log Source = WinEventLog AND EventCode = 4688 AND NewProcessName CONTAINS "whoami"
      THEN trigger alert → "WHOAMI Command Execution Detected"
```

**Other Rule Examples:**

| Condition                                | Alert                                                   |
| ---------------------------------------- | ------------------------------------------------------- |
| 5 failed logins within 10 seconds        | `Multiple Failed Login Attempts`                        |
| Successful login after multiple failures | `Successful Login After Multiple Failed Attempts`       |
| USB device plugged in                    | `USB Device Connected` (if USB is restricted by policy) |
| Outbound traffic > 25 MB                 | `Potential Data Exfiltration Attempt`                   |

---

## 7. Alert Investigation Process

Analysts spend most of their time on **SIEM dashboards**, which summarize key network activity. When an alert fires, the investigation follows this flow:

```
Alert triggered in SIEM
         ↓
Analyst examines the events/flows associated with the alert
         ↓
Analyst checks which rule conditions were met
         ↓
Determine: False Positive or True Positive?
    │
    ├── False Positive
    │       → Tune the detection rule to prevent recurrence
    │
    └── True Positive
            → Perform deeper investigation
            → Contact the asset owner to verify the activity
            → Confirmed suspicious? → Isolate the infected host
            → Block the suspicious IP
```

---

## 8. Quick Reference

### The Problem SIEM Solves

| Problem                            | SIEM Solution                                    |
| ---------------------------------- | ------------------------------------------------ |
| Logs scattered across many devices | Centralized log collection via agents/APIs       |
| Different log formats per device   | Parsing + Normalization into a consistent format |
| Individual logs lack context       | Correlation across multiple log sources          |
| Too many logs to analyze manually  | Detection rules + automated alerting             |
| Slow incident response             | Real-time dashboards and instant alerts          |

### SIEM Core Concepts at a Glance

| Concept               | Definition                                                                              |
| --------------------- | --------------------------------------------------------------------------------------- |
| **Parsing**           | Breaking a raw log into individual fields (IP, user, timestamp, event type, etc.).      |
| **Normalization**     | Converting logs from all sources into one consistent unified format.                    |
| **Correlation**       | Finding relationships between events across different log sources to identify patterns. |
| **Detection Rule**    | A logical condition (field-value expression) that triggers an alert when matched.       |
| **Agent / Forwarder** | A lightweight tool installed on endpoints that captures and sends logs to the SIEM.     |
| **Syslog**            | Standard protocol for real-time log forwarding to a centralized destination.            |
| **Dashboard**         | Visual summary of SIEM data — primary interface for analyst monitoring.                 |

### Linux Log Paths Reference

```
/var/log/httpd          → HTTP requests/responses and errors
/var/log/cron           → Cron job events
/var/log/auth.log       → Authentication logs (Debian/Ubuntu)
/var/log/secure         → Authentication logs (RHEL/CentOS)
/var/log/kern           → Kernel events
/var/log/apache         → Apache web server logs
/var/log/httpd          → Apache web server logs (RHEL/CentOS)
```

### Key Windows Event IDs for SIEM Rules

```
104  → Event log cleared (attacker covering tracks)
4624 → Successful login
4625 → Failed login
4688 → New process created (used to detect command execution)
4720 → User account created
4726 → User account deleted
```

### Key Terminology

| Term                     | Definition                                                                                                |
| ------------------------ | --------------------------------------------------------------------------------------------------------- |
| **SIEM**                 | Security Information and Event Management — collects, normalizes, correlates logs, and alerts on threats. |
| **Host-Centric Logs**    | Logs generated by events within a specific host (file access, authentication, process execution).         |
| **Network-Centric Logs** | Logs generated by network activity between hosts or with the internet (traffic, VPN, firewall).           |
| **Log Ingestion**        | The process of collecting and importing logs into the SIEM from various sources.                          |
| **False Positive**       | An alert triggered by benign activity — requires rule tuning to reduce recurrence.                        |
| **True Positive**        | An alert confirmed to represent genuine malicious or suspicious activity.                                 |
| **Rule Tuning**          | Adjusting detection rule conditions to reduce false positives without losing detection coverage.          |
| **Splunk**               | One of the most popular commercial SIEM solutions — uses "forwarders" for log ingestion.                  |
| **ELK**                  | Open-source SIEM stack (Elasticsearch, Logstash, Kibana) — supports manual log upload.                    |

---

## 📝 My Notes & Questions

> *[ Write your questions, insights, and connections to other concepts here ]*

---

*TryHackMe — Cybersecurity 101 | SIEM*
