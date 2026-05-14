# 🛡️ TryHackMe — Cybersecurity 101

## Module: Incident Response Fundamentals | Study Summary

> **Keywords:** Incident Response · Events · Alerts · False Positive · True Positive · SANS · NIST · PICERL · Incident Severity · Playbook · Runbook · SIEM · EDR · Antivirus

---

## 📚 Table of Contents

1. [Events, Alerts, and Incidents](#1-events-alerts-and-incidents)
2. [Types of Incidents](#2-types-of-incidents)
3. [Incident Response Frameworks](#3-incident-response-frameworks)
4. [SANS vs NIST — Comparison](#4-sans-vs-nist--comparison)
5. [Incident Response Plan](#5-incident-response-plan)
6. [Incident Response Techniques & Tools](#6-incident-response-techniques--tools)
7. [Playbooks and Runbooks](#7-playbooks-and-runbooks)
8. [Quick Reference](#8-quick-reference)

---

## 1. Events, Alerts, and Incidents

Understanding the distinction between these three terms is fundamental to incident response:

### Events

Every process running on a device — interactive (user-initiated) or non-interactive (background) — generates **events**. Events are logs of what each process did. They are generated in huge numbers constantly across every device in an organization.

### Alerts

Security solutions ingest events as logs and apply detection logic to identify potentially harmful activity. When suspicious patterns are found, the solution triggers an **alert**. Analysts then examine these alerts, which fall into two categories:

| Category | Definition | Example |
|---|---|---|
| **False Positive** | Alert points to something suspicious but turns out to be harmless. | High data transfer flagged — investigation reveals it was a scheduled cloud backup. |
| **True Positive** | Alert points to something suspicious and is confirmed as genuinely harmful. | Phishing email flagged — investigation confirms it was a real phishing attempt targeting a user. |

### Incidents

**True positive alerts are classified as incidents.** Once an alert is confirmed as an incident, it is assigned a **severity level** to prioritize the response:

```
Critical → High → Medium → Low
```

Critical severity incidents always take priority. Multiple simultaneous incidents are triaged and handled in order of severity.

---

## 2. Types of Incidents

Security incidents are not all the same. They fall into distinct categories, each with a different impact profile. The same type of incident can be catastrophic for one organization and minor for another depending on what they rely on.

| Type | Description | Key Risk |
|---|---|---|
| **Malware Infections** | Malicious programs (viruses, ransomware, trojans, etc.) that damage systems, networks, or applications. Most commonly delivered via malicious files (documents, executables). | System damage, data encryption, lateral movement |
| **Security Breaches** | Unauthorized access to confidential data — someone gains access to information they are not permitted to see. | Exposure of sensitive organizational or personal data |
| **Data Leaks** | Confidential information is exposed to unauthorized entities. Can be **intentional** (attacker-driven) or **unintentional** (human error, misconfiguration). | Reputational damage, extortion, regulatory penalties |
| **Insider Attacks** | Malicious activity originating from within the organization — e.g. a disgruntled employee infecting the network via a USB on their last day. Insiders have greater internal access than external attackers. | Highly damaging due to privileged access |
| **Denial of Service (DoS)** | Attacker floods a system, network, or application with fake requests, exhausting its resources until it becomes unavailable to legitimate users. Targets the **Availability** pillar of the CIA triad. | Service unavailability, revenue loss, operational disruption |

> 💡 Incidents can occur independently or in combination within the same victim environment.

---

## 3. Incident Response Frameworks

Due to the varied and complex nature of incidents, a **structured process** is essential for effective response. **Incident Response Frameworks** provide generic, repeatable approaches applicable to any incident.

The two most widely used frameworks are **SANS** and **NIST**, both of which are very similar in structure.

### SANS Framework — 6 Phases (PICERL)

The acronym **PICERL** helps remember all six phases in order:

| # | Phase | What happens | Example |
|---|---|---|---|
| 1 | **P**reparation | Build the resources, teams, plans, and tools needed to handle incidents before they occur. Includes security awareness training. | Conducting phishing awareness training for all employees. |
| 2 | **I**dentification | Detect and confirm abnormal behavior that indicates an incident is occurring. | Security team detects unusual outbound data transfer — confirms compromise after malicious file download from phishing email. |
| 3 | **C**ontainment | Minimize the impact of the confirmed incident. Stop the spread before addressing the root cause. | Isolate the compromised host from the network. Disable compromised user accounts. |
| 4 | **E**radication | Remove the threat completely from the affected environment. | Run a deep malware scan and remove the malicious software from the host. |
| 5 | **R**ecovery | Restore affected systems to normal operation from backup or by rebuilding. Test before returning to production. | Reconfigure the compromised host and restore exfiltrated data from backup. |
| 6 | **L**essons Learned | Review the incident to identify gaps in detection and response. Document improvements to prevent recurrence. | Post-incident review meeting to analyze root cause and improve security posture. |

---

## 4. SANS vs NIST — Comparison

The **NIST framework** condenses the 6 SANS phases into 4 by merging some steps:

| SANS (6 phases) | NIST (4 phases) |
|---|---|
| Preparation | Preparation |
| Identification | Detection and Analysis |
| Containment | Containment, Eradication, and Recovery |
| Eradication | Containment, Eradication, and Recovery |
| Recovery | Containment, Eradication, and Recovery |
| Lessons Learned | Post-Incident Activity |

> 💡 Both frameworks lead to the same outcome — the main difference is how the phases are grouped. SANS provides more granularity; NIST is more concise. Organizations typically adapt one of these frameworks to create their own internal incident response process.

---

## 5. Incident Response Plan

Every organization should have a formal **Incident Response Plan (IRP)** — a structured document that outlines the approach to follow before, during, and after any incident. It must be **formally approved by senior management**.

### Key Components of an Incident Response Plan

- **Roles and Responsibilities** — who does what during an incident
- **Incident Response Methodology** — which framework is followed (SANS / NIST / custom)
- **Communication Plan** — how stakeholders are notified, including law enforcement when required
- **Escalation Path** — clear chain of escalation from L1 analysts up to management and executive leadership

---

## 6. Incident Response Techniques & Tools

The **Identification** phase (SANS) / **Detection and Analysis** phase (NIST) is supported by security solutions that automate the detection of incidents across the environment:

| Tool | Role in Incident Response |
|---|---|
| **SIEM** | Collects logs from all sources in one centralized location. Correlates them to identify incidents. Primary detection tool. |
| **Antivirus (AV)** | Detects known malicious programs on endpoints. Regularly scans systems for threats. |
| **EDR** | Deployed on every endpoint. Detects advanced threats, and can also **contain and eradicate** them — extends beyond detection into response. |

> 💡 **EDR is unique** among these three because it can act across multiple phases — not just detection but also containment and eradication — making it the most capable individual tool in the IR process.

---

## 7. Playbooks and Runbooks

Once an incident is identified, responders need clear instructions for how to handle it. Two types of documents serve this purpose:

### Playbooks

**Playbooks** are **high-level guidelines** for a comprehensive incident response. They outline the overall approach and key steps for a specific type of incident.

**Example — Phishing Email Playbook:**

1. Notify all stakeholders of the phishing email incident
2. Determine if the email was malicious via header and body analysis
3. Identify and analyze any attachments
4. Determine if anyone opened the attachments
5. Isolate infected systems from the network
6. Block the email sender

### Runbooks

**Runbooks** are **detailed, step-by-step execution instructions** for specific tasks within an incident response. They are more granular than playbooks and may vary depending on the tools and resources available.

| | Playbook | Runbook |
|---|---|---|
| **Level** | High-level guidelines | Detailed step-by-step execution |
| **Scope** | Whole incident type | Specific task within an incident |
| **Audience** | IR team overall | Analyst executing a specific step |

---

## 8. Quick Reference

### Event → Alert → Incident Flow

```
Processes generate Events
         ↓
Security solutions ingest Events as logs
         ↓
Detection logic triggers Alerts
         ↓
Analyst reviews Alert
    ├── False Positive → Close / document
    └── True Positive → Classify as Incident
                              ↓
                    Assign Severity (Critical / High / Medium / Low)
                              ↓
                    Initiate Incident Response (PICERL)
```

### PICERL at a Glance

```
P — Preparation      → Build teams, tools, plans, run training
I — Identification   → Detect and confirm the incident
C — Containment      → Stop the spread, isolate affected systems
E — Eradication      → Remove the threat completely
R — Recovery         → Restore systems, test, return to production
L — Lessons Learned  → Review, document gaps, improve
```

### Incident Types Summary

| Incident | Core Impact |
|---|---|
| Malware Infection | System damage, data encryption, lateral spread |
| Security Breach | Unauthorized access to confidential data |
| Data Leak | Confidential data exposed — intentional or accidental |
| Insider Attack | Internal actor with privileged access causes damage |
| DoS Attack | Service unavailability — targets Availability |

### Key Terminology

| Term | Definition |
|---|---|
| **Event** | A log entry generated by any process on a device recording what it did. |
| **Alert** | Notification triggered by a security solution when events match a detection rule. |
| **False Positive** | An alert that appears harmful but is confirmed to be benign after investigation. |
| **True Positive** | An alert that is confirmed to be a genuine security incident. |
| **Incident** | A confirmed true positive alert requiring a structured response. |
| **Incident Severity** | Classification (Critical / High / Medium / Low) that determines response priority. |
| **PICERL** | Acronym for the SANS 6-phase IR framework: Preparation, Identification, Containment, Eradication, Recovery, Lessons Learned. |
| **Incident Response Plan** | Formal document approved by senior management outlining the organization's IR methodology, roles, communication, and escalation paths. |
| **Playbook** | High-level guidelines for responding to a specific type of incident. |
| **Runbook** | Detailed step-by-step execution instructions for a specific task within an incident response. |
| **Containment** | IR phase focused on stopping the spread of an incident and minimizing its impact. |
| **Eradication** | IR phase focused on completely removing the threat from the environment. |
| **Lessons Learned** | Final IR phase — post-incident review to identify gaps and improve future response. |

---

## 📝 My Notes & Questions

> *[ Write your questions, insights, and connections to other concepts here ]*

---

*TryHackMe — Cybersecurity 101 | Incident Response Fundamentals*
