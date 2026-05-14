# 🛡️ TryHackMe — Cybersecurity 101

## Module: SOC Basics | Study Summary

> **Keywords:** SOC · Detection · Response · Alert Triage · SIEM · EDR · Firewall · Incident Response · People · Process · Technology

---

## 📚 Table of Contents

1. [What is a SOC](#1-what-is-a-soc)
2. [The Three Pillars: People, Process, Technology](#2-the-three-pillars-people-process-technology)
3. [People — SOC Team Roles](#3-people--soc-team-roles)
4. [Process — SOC Workflows](#4-process--soc-workflows)
5. [Technology — Security Solutions](#5-technology--security-solutions)
6. [Quick Reference](#6-quick-reference)

---

## 1. What is a SOC

A **Security Operations Center (SOC)** is a centralized team and environment whose main focus is keeping **Detection** and **Response** intact across an entire organization's network and systems. The SOC monitors everything from one centralized location on a continuous basis.

### Detection Responsibilities

| Area | Description |
|---|---|
| **Detect vulnerabilities** | Identify weaknesses in software or systems (OS, applications, servers) before attackers can exploit them. Not always the SOC's primary responsibility, but unfixed vulnerabilities affect the entire organization's security posture. |
| **Detect unauthorized activity** | Identify when valid credentials are being misused — e.g. an attacker logging in with a stolen username and password. Clues like geographic location anomalies help flag this. |
| **Detect policy violations** | Identify when employees break security rules — e.g. downloading pirated software, sending confidential files insecurely. What constitutes a violation varies by organization. |
| **Detect intrusions** | Identify unauthorized access to systems and networks — e.g. an attacker exploiting a web application, or a user visiting a malicious site and getting infected. |

### Response Responsibilities

| Area | Description |
|---|---|
| **Support incident response** | Once an incident is detected, the SOC supports the response team in minimizing its impact and performing root cause analysis — including containment, eradication, and recovery. |

---

## 2. The Three Pillars: People, Process, Technology

A mature SOC environment is built on three interdependent pillars. All three must coexist for the SOC to function effectively:

```
        PEOPLE
       /       \
      /  M A T  \
     /  U R E    \
PROCESS ———————— TECHNOLOGY
```

| Pillar | Role |
|---|---|
| **People** | The SOC team — skilled professionals who interpret alerts, investigate incidents, and make judgment calls that automation cannot. |
| **Process** | The structured workflows and procedures the team follows — alert triage, escalation, reporting, incident response. |
| **Technology** | The security solutions (SIEM, EDR, Firewall, etc.) that centralize data, automate detection, and enable response at scale. |

> 💡 **The fire brigade analogy:** A centralized fire alarm system (Technology) receiving hundreds of simultaneous alerts is useless without firefighters (People) who can evaluate which alarms are real fires vs. smoke from cooking — and without a dispatch protocol (Process) to prioritize which ones to respond to first.

---

## 3. People — SOC Team Roles

Regardless of how much security is automated, human judgment remains essential. The SOC team is structured in a hierarchy where alerts escalate from first responders to experienced analysts to management.

![SOC Team Hierarchy](https://tryhackme-images.s3.amazonaws.com/user-uploads/6645aa8c024f7893371eb7ac/room-content/6645aa8c024f7893371eb7ac-1718872774537)

| Role | Responsibilities |
|---|---|
| **SOC Analyst — Level 1** | First responders. All detections pass through L1 first. Perform basic **alert triage** to determine if an alert is genuinely harmful. Escalate through proper channels. |
| **SOC Analyst — Level 2** | Deeper investigation. Handle alerts escalated from L1. **Correlate data from multiple sources** to perform thorough analysis. |
| **SOC Analyst — Level 3** | Most experienced. **Proactively hunt for threat indicators** (threat hunting). Handle critical severity incidents — containment, eradication, recovery. Support incident response activities. |
| **Security Engineer** | Deploy and configure the security solutions (SIEM, EDR, Firewall, etc.) that the analyst team operates on. Ensures tools run smoothly. |
| **Detection Engineer** | Build and maintain the **detection rules** — the logic behind security solutions that identifies harmful activities. Often fulfilled by L2/L3 analysts or as a dedicated role. |
| **SOC Manager** | Manages the processes the team follows. Provides support to the team. Interfaces with the **CISO** (Chief Information Security Officer) to report on security posture and efforts. |

> 💡 SOC team composition varies by organization size and criticality. Smaller teams may have one person covering multiple roles; larger teams may have dedicated specialists for each.

---

## 4. Process — SOC Workflows

### Alert Triage

Alert triage is the **foundation of SOC operations** — the first response to any alert. Its purpose is to analyze the alert, determine its severity, and prioritize it appropriately.

Triage is structured around answering the **5 Ws**:

| W | Question | Example Answer |
|---|---|---|
| **What?** | What happened? | A malicious file was detected on a host inside the network. |
| **When?** | When did it happen? | The file was detected at 13:20 on June 5, 2024. |
| **Where?** | Where did it happen? | Detected on host: "GEORGE PC", in a specific directory. |
| **Who?** | Who is affected? | The file was detected for user George. |
| **Why?** | Why did it happen? | George downloaded the file from a pirated software website to use paid software for free. |

> 📝 The 5 Ws framework is equally critical when **documenting** an alert or incident — they form the backbone of any security report.

### Reporting

Harmful alerts must be **escalated as tickets** to higher-level analysts for timely resolution. A good report includes:

- All 5 Ws answered
- Thorough analysis of the alert
- Screenshots as evidence of the activity

### Incident Response & Forensics

When escalated detections point to **highly malicious or critical activity**, higher-level teams initiate a formal **incident response**. This structured process covers:

- **Containment** — stopping the spread of the incident
- **Eradication** — removing the threat from the environment
- **Recovery** — restoring affected systems to normal operation

In some cases, a **forensics investigation** is also conducted to determine the root cause by analyzing artifacts from affected systems or network traffic.

> 🔗 [Incident Response room — TryHackMe](https://tryhackme.com/r/room/incidentresponsefundamentals)

---

## 5. Technology — Security Solutions

Security solutions form the **Technology** pillar of the SOC. They centralize information from all devices and applications across the network and automate detection and response capabilities — dramatically reducing the manual effort required.

### Core Security Solutions

#### SIEM — Security Information and Event Management

The **most widely deployed tool in SOC environments**. SIEM is the central hub of detection.

- **Collects logs** from all devices and applications across the network (log sources)
- **Detection rules** are configured in the SIEM — logic that identifies suspicious patterns
- **Correlates** data from multiple log sources and **alerts** when a rule match is found
- Modern SIEMs also provide **user behavior analytics (UBA)** and **threat intelligence** integration, supported by machine learning

> ⚠️ SIEM provides **Detection** capabilities only — it does not perform automated response actions.

#### EDR — Endpoint Detection and Response

Operates at the **endpoint level** (individual devices — laptops, servers, workstations).

- Provides **real-time and historical visibility** of device activity
- **Extensive detection capabilities** for endpoint threats
- Can carry out **automated responses** at the endpoint level
- Enables deep investigation and response **with a few clicks**

#### Firewall

Operates at the **network level** — acts as a barrier between internal and external networks (internet).

- **Monitors incoming and outgoing traffic**
- **Filters unauthorized traffic** — blocks what doesn't meet the defined rules
- Has **detection rules** to identify and block suspicious traffic **before it reaches the internal network**

### Other Security Solutions in a SOC

Beyond the three core tools, a SOC may deploy additional solutions depending on the organization's threat surface and resources:

| Solution | Focus Area |
|---|---|
| **Antivirus** | Endpoint malware detection and removal |
| **EPP** (Endpoint Protection Platform) | Preventive endpoint security |
| **IDS/IPS** (Intrusion Detection/Prevention System) | Network-level threat detection and blocking |
| **XDR** (Extended Detection and Response) | Unified detection and response across endpoints, network, and cloud |
| **SOAR** (Security Orchestration, Automation and Response) | Automates repetitive SOC tasks and orchestrates response workflows |

---

## 6. Quick Reference

### SOC Detection vs Response

| Function | What it covers |
|---|---|
| **Detection** | Vulnerabilities, unauthorized activity, policy violations, intrusions |
| **Response** | Incident support, containment, eradication, recovery, forensics |

### Tool Responsibilities at a Glance

| Tool | Detection | Response | Level |
|---|---|---|---|
| **SIEM** | ✅ Yes | ❌ No | Network-wide |
| **EDR** | ✅ Yes | ✅ Yes | Endpoint |
| **Firewall** | ✅ Yes (filtering) | ✅ Yes (blocking) | Network perimeter |

### Escalation Path

```
Alert generated by security solution (SIEM / EDR / Firewall)
          ↓
SOC Analyst L1 — basic triage, 5 Ws, determine severity
          ↓ (if deeper investigation needed)
SOC Analyst L2 — multi-source correlation, detailed analysis
          ↓ (if critical severity)
SOC Analyst L3 — incident response, containment, eradication, recovery
          ↓
SOC Manager → CISO (status updates and posture reporting)
```

### Key Terminology

| Term | Definition |
|---|---|
| **SOC** | Security Operations Center — centralized team and environment for continuous threat detection and response. |
| **Alert triage** | The first-response process of analyzing an alert to determine its severity and whether it is genuinely harmful. |
| **5 Ws** | What, When, Where, Who, Why — the framework used to analyze and document any security alert or incident. |
| **SIEM** | Security Information and Event Management — collects logs, applies detection rules, and alerts on suspicious activity. Detection only. |
| **EDR** | Endpoint Detection and Response — provides endpoint-level visibility, detection, and automated response. |
| **Firewall** | Network security barrier that monitors and filters traffic between internal and external networks. |
| **CISO** | Chief Information Security Officer — the executive responsible for an organization's overall security posture. |
| **Threat hunting** | Proactive search for threat indicators across the environment — primarily an L3 SOC Analyst responsibility. |
| **Incident response** | Structured process to contain, eradicate, and recover from a confirmed security incident. |
| **Forensics** | Detailed investigation of system or network artifacts to determine the root cause of an incident. |
| **SOAR** | Security Orchestration, Automation and Response — automates repetitive SOC tasks and response workflows. |

---

## 📝 My Notes & Questions

> *[ Write your questions, insights, and connections to other concepts here ]*

---

*TryHackMe — Cybersecurity 101 | SOC Basics*
