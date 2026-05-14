# 🛡️ TryHackMe — Cybersecurity 101
## Module: MITRE ATT&CK & Frameworks | Study Summary
> **Keywords:** MITRE · ATT&CK · TTPs · Tactics · Techniques · Procedures · CAR · D3FEND · Caldera · ATLAS · AADAPT · Threat Intelligence · Navigator

---

## 📚 Table of Contents

1. [ATT&CK Framework Overview](#1-attck-framework-overview)
2. [ATT&CK Matrix & Navigation](#2-attck-matrix--navigation)
3. [ATT&CK in Operation — Who Uses It and How](#3-attck-in-operation--who-uses-it-and-how)
4. [ATT&CK for Threat Intelligence — Practical Scenario](#4-attck-for-threat-intelligence--practical-scenario)
5. [Cyber Analytics Repository (CAR)](#5-cyber-analytics-repository-car)
6. [MITRE D3FEND Framework](#6-mitre-d3fend-framework)
7. [Other MITRE Projects](#7-other-mitre-projects)
8. [Quick Reference Cheatsheet](#8-quick-reference-cheatsheet)
9. [Key Terminology](#9-key-terminology)
10. [My Notes & Questions](#10-my-notes--questions)

---

## 1. ATT&CK Framework Overview

**MITRE ATT&CK®** (Adversarial Tactics, Techniques, and Common Knowledge) is a globally-accessible knowledge base documenting adversary tactics and techniques based on **real-world observations**. It was created in 2013 when MITRE recognized the need to document and categorize the standard TTPs used by Advanced Persistent Threat (APT) groups.

🔗 [MITRE ATT&CK Official Site](https://attack.mitre.org/)

### What is TTP?

| Component | Definition | The Question it Answers |
|---|---|---|
| **Tactic** | The adversary's **goal or objective** | *Why* are they doing this? |
| **Technique** | **How** an adversary achieves their goal | *What method* do they use? |
| **Procedure** | The specific **implementation** of a technique | *Exactly how* is it executed? |

### ATT&CK Evolution

ATT&CK started focused on **Windows** and has expanded significantly:

| Matrix | Coverage |
|---|---|
| **Enterprise** | Windows, macOS, Linux, Cloud platforms, Containers, Network |
| **Mobile** | Android, iOS |
| **ICS** | Industrial Control Systems |

> The framework is continuously expanded through contributions from the global cyber security community. Both **defenders** (detect & respond) and **red teams** (plan realistic attack simulations) rely on it.

---

## 2. ATT&CK Matrix & Navigation

The **ATT&CK Matrix** is a visual representation of all tactics and techniques. Tactics appear across the top; techniques are nested below each tactic and can be expanded to reveal sub-techniques.

🔗 [ATT&CK Matrix](https://attack.mitre.org/matrices/)
🔗 [ATT&CK Navigator](https://mitre-attack.github.io/attack-navigator/) — tool for annotating and exploring matrices

### Matrix Structure — Example

![A portion of the MITRE ATT&CK matrix with the Reconnaissance tactic highlighted, as well as its techniques and subtechniques](https://tryhackme-images.s3.amazonaws.com/user-uploads/616945d482ef350052080da1/room-content/616945d482ef350052080da1-1760433073448.svg)

| Level | Example |
|---|---|
| **Tactic** | Reconnaissance — *the attacker's goal* |
| **Technique** | Active Scanning (T1595) — *how they achieve it* |
| **Sub-technique** | Scanning IP Blocks / Vulnerability Scanning / Wordlist Scanning |

### Technique Detail Pages

Each technique page includes:
- Technique **ID** (e.g., T1595)
- Description
- **Sub-techniques** listed
- **Procedure examples** (groups, software, campaigns that used it)
- **Mitigations**
- **Detections**
- References

![The Active Scanning technique from MITRE's ATT&CK framework highlighting the technique, subtechniques, description, and relevant information](https://tryhackme-images.s3.amazonaws.com/user-uploads/616945d482ef350052080da1/room-content/616945d482ef350052080da1-1760437867245.svg)

---

## 3. ATT&CK in Operation — Who Uses It and How

ATT&CK provides a **standard, consistent language** for describing adversary behavior across the security community — solving the problem of the same technique being called different things by different teams or vendors.

### Who Uses ATT&CK?

| Role | Goal | How They Use ATT&CK |
|---|---|---|
| **CTI Teams** (Cyber Threat Intelligence) | Collect and analyze threat information | Map observed threat actor behavior to TTPs — create actionable profiles |
| **SOC Analysts** | Investigate and triage security alerts | Link activity to tactics/techniques to provide context and prioritize incidents |
| **Detection Engineers** | Design and improve detection systems | Map SIEM/EDR rules to ATT&CK for better detection coverage |
| **Incident Responders** | Respond to and investigate security incidents | Map incident timelines to tactics/techniques to visualize the full attack |
| **Red & Purple Teams** | Emulate adversary behavior to test defenses | Build emulation plans aligned with known group operations |

### Mapping in Action — Mustang Panda (G0129)

Mustang Panda is an APT group that has targeted government entities, non-profits, and NGOs. Their TTPs have been mapped and visualized using the ATT&CK Navigator:

🔗 [Mustang Panda ATT&CK Page](https://attack.mitre.org/groups/G0129/)
🔗 [Mustang Panda Navigator Layer](https://mitre-attack.github.io/attack-navigator//#layerURL=https%3A%2F%2Fattack.mitre.org%2Fgroups%2FG0129%2FG0129-enterprise-layer.json)

![The MITRE ATT&CK matrix for the threat group Mustang Panda G0129](https://tryhackme-images.s3.amazonaws.com/user-uploads/616945d482ef350052080da1/room-content/616945d482ef350052080da1-1760615949921.svg)

**Mustang Panda behavior summary:**

| ATT&CK Category | Behavior |
|---|---|
| Initial Access | Phishing techniques |
| Persistence | Scheduled Tasks |
| Defense Evasion | Obfuscates files |
| Command & Control | Ingress Tool Transfer |

> **Key takeaway:** After an incident, mapping the attack timeline to ATT&CK tactics and techniques helps teams better prepare for future campaigns from the same group or using the same TTPs.

---

## 4. ATT&CK for Threat Intelligence — Practical Scenario

**Scenario:** You are a security analyst in the **aviation sector**. Your organization is migrating to the cloud. You must use ATT&CK to research APT groups known to target aviation and identify potential defensive gaps.

**Workflow:**
1. Go to 🔗 [ATT&CK Groups](https://attack.mitre.org/groups/)
2. Search for groups known to target your sector
3. Open the group's Navigator layer to visualize their TTPs
4. Identify high-risk techniques — especially those targeting your new cloud environment
5. Cross-reference with mitigations and detection strategies

**What to look for per technique page:**
- Sub-techniques relevant to your environment (e.g., cloud-specific)
- Related tools/software the group uses
- Recommended mitigations
- Detection strategies and their IDs

---

## 5. Cyber Analytics Repository (CAR)

**CAR** is a knowledge base of **ready-made detection analytics** developed by MITRE, built around the ATT&CK adversary model. It bridges the gap between knowing *what* a technique is and knowing *how to detect it*.

🔗 [CAR Official Site](https://car.mitre.org/)
🔗 [CAR Analytics List](https://car.mitre.org/analytics/)
🔗 [CAR ATT&CK Navigator Layer](https://mitre-attack.github.io/attack-navigator/#layerURL=https://raw.githubusercontent.com/mitre-attack/car/master/docs/coverage/car_analytic_coverage_04_05_2022.json)

### What CAR Provides

| Component | Description |
|---|---|
| **Description** | Explains what the analytic detects and why |
| **ATT&CK Mapping** | Links the analytic to specific tactics and techniques |
| **Pseudocode** | Human-readable logic of the detection |
| **Tool Implementations** | Ready-made queries for Splunk, EQL, LogPoint, etc. |
| **Unit Tests** | Validation tests to confirm the analytic works as intended |

### CAR Example — CAR-2020-09-001: Scheduled Task File Access

🔗 [CAR-2020-09-001](https://car.mitre.org/analytics/CAR-2020-09-001/)

![A screenshot of the Cyber Analytics Repository technique CAR202009001 Scheduled Task  File Access](https://tryhackme-images.s3.amazonaws.com/user-uploads/616945d482ef350052080da1/room-content/616945d482ef350052080da1-1760148322681.svg)

The **Implementations** section provides Pseudocode, Splunk queries, and LogPoint searches — allowing analysts to immediately translate this ATT&CK technique into actionable SIEM detections:

![A screenshot of the Implementations section of the Cyber Analytics Repository technique CAR202009001 Scheduled Task  File Access](https://tryhackme-images.s3.amazonaws.com/user-uploads/616945d482ef350052080da1/room-content/616945d482ef350052080da1-1760148322761.svg)

> **In other words:** CAR takes ATT&CK intelligence and turns it into code you can actually deploy in your SIEM. You don't have to write detections from scratch — CAR gives you validated starting points.

> **Analytic Types in CAR:** Each analytic is classified by type (e.g., TTP, Situational Awareness). This tells you whether a detection is directly tied to an adversary technique or provides broader visibility.

---

## 6. MITRE D3FEND Framework

**D3FEND** (Detection, Denial, and Disruption Framework Empowering Network Defense) is the **defensive counterpart to ATT&CK**. While ATT&CK documents *how attacks happen*, D3FEND maps out *how to stop them* — providing a structured taxonomy of defensive techniques with a common language for describing how security controls work.

🔗 [D3FEND Official Site](https://d3fend.mitre.org/)
🔗 [D3FEND Matrix](https://d3fend.mitre.org/)

### D3FEND Matrix — Seven Tactics

![The MITRE D3FEND matrix showing the seven tactics Model, Harden, Detect, Isolate, Deceive, Evict, and Restore](https://tryhackme-images.s3.amazonaws.com/user-uploads/616945d482ef350052080da1/room-content/616945d482ef350052080da1-1760147981536.svg)

| D3FEND Tactic | Purpose |
|---|---|
| **Model** | Understand the environment — asset inventory, dependency mapping |
| **Harden** | Reduce attack surface — configuration, credential management |
| **Detect** | Identify malicious activity — behavioral analysis, file monitoring |
| **Isolate** | Limit attacker movement — network isolation, sandboxing |
| **Deceive** | Mislead attackers — honeypots, decoy accounts |
| **Evict** | Remove attacker presence — credential rotation, process termination |
| **Restore** | Recover from incidents — backup restoration, reimaging |

### D3FEND Technique Pages

Each D3FEND technique page includes:
- **Definition** and how the defense works
- **Digital Artifact Relationships** (what data/artifacts the defense analyzes or produces)
- **Links to related ATT&CK techniques** (attacker move ↔ defender countermeasure)
- Implementation considerations

**Example — Credential Rotation (D3-CRO):**

🔗 [Credential Rotation D3-CRO](https://d3fend.mitre.org/technique/d3f:CredentialRotation/)

![A screenshot showing MITRE D3FEND technique Credential Rotation D3CRO, including its definition and how it works, and the technique's Digital Artifact Relationships tree](https://tryhackme-images.s3.amazonaws.com/user-uploads/616945d482ef350052080da1/room-content/616945d482ef350052080da1-1760148135498.svg)

> **ATT&CK ↔ D3FEND pairing:** D3FEND explicitly links defensive techniques to the ATT&CK techniques they counter. This allows you to see both sides: the attacker's move and the defender's countermeasure in the same view.

---

## 7. Other MITRE Projects

### Adversary Emulation Library

A free collection of **step-by-step emulation plans** that mimic real-world attacks by known threat groups. Maintained by the **Center for Threat-Informed Defense (CTID)**.

🔗 [Adversary Emulation Library](https://ctid.mitre.org/resources/adversary-emulation-library/)
🔗 [Library on GitHub](https://github.com/center-for-threat-informed-defense/adversary_emulation_library)
🔗 [CTID](https://ctid.mitre.org/)

> Use case: Red teams and purple teams use these plans to simulate specific APT group behavior in a controlled way to test how well an organization's defenses would hold up against that group.

---

### Caldera

![The MITRE Caldera logo](https://tryhackme-images.s3.amazonaws.com/user-uploads/616945d482ef350052080da1/room-content/616945d482ef350052080da1-1760576813680.png)

**Caldera** is an **automated adversary emulation tool** that simulates real-world attacker behavior using the ATT&CK framework. It supports both offensive and defensive operations.

🔗 [Caldera Official Site](https://caldera.mitre.org/)

| Use Case | Description |
|---|---|
| **Red Team** | Simulate attacker behavior to find gaps |
| **Blue Team** | Evaluate detection methods and alerts |
| **Purple Team** | Collaborative exercises testing both attack and defense |
| **Incident Response Training** | Practice responding in a controlled environment |

---

### AADAPT

**AADAPT** (Adversarial Actions in Digital Asset Payment Technologies) is a knowledge base covering adversary tactics and techniques targeting **digital asset management systems** — blockchain networks, smart contracts, digital wallets, and other digital asset technologies.

🔗 [AADAPT Official Site](https://aadapt.mitre.org/)

> Follows the same structural format as ATT&CK. Designed for defenders working in fintech, crypto, and digital asset environments.

---

### ATLAS

**ATLAS** (Adversarial Threat Landscape for Artificial-Intelligence Systems) is a knowledge base and framework focusing on threats targeting **AI and machine learning systems**. It documents real-world attack techniques, vulnerabilities, and mitigations specific to AI technology.

🔗 [ATLAS Official Site](https://atlas.mitre.org/)
🔗 [ATLAS Matrix](https://atlas.mitre.org/matrices/ATLAS)

> As AI systems become more common in organizations, ATLAS provides the vocabulary and structure needed to reason about and defend against AI-specific attacks (e.g., prompt injection, model poisoning, adversarial examples).

---

### MITRE Projects Summary Table

| Project | Focus Area | Primary Users |
|---|---|---|
| **ATT&CK** | Adversary TTPs across Enterprise, Mobile, ICS | All security teams |
| **CAR** | Detection analytics mapped to ATT&CK | Detection Engineers, SOC Analysts |
| **D3FEND** | Defensive techniques and countermeasures | Defenders, Architects |
| **Adversary Emulation Library** | Step-by-step APT emulation plans | Red/Purple Teams |
| **Caldera** | Automated adversary emulation platform | Red/Blue/Purple Teams |
| **AADAPT** | Threats to digital asset / crypto systems | Fintech defenders |
| **ATLAS** | Threats to AI and ML systems | AI/ML security teams |

---

## 8. Quick Reference Cheatsheet

### ATT&CK Key Links

```
ATT&CK Home:       https://attack.mitre.org/
Tactics:           https://attack.mitre.org/tactics/enterprise/
Techniques:        https://attack.mitre.org/techniques/enterprise/
Groups:            https://attack.mitre.org/groups/
Navigator:         https://mitre-attack.github.io/attack-navigator/
Mobile Matrix:     https://attack.mitre.org/matrices/mobile/
ICS Matrix:        https://attack.mitre.org/matrices/ics/
```

### CAR Key Links

```
CAR Home:          https://car.mitre.org/
Analytics List:    https://car.mitre.org/analytics/
CAR Navigator:     https://mitre-attack.github.io/attack-navigator/#layerURL=https://raw.githubusercontent.com/mitre-attack/car/master/docs/coverage/car_analytic_coverage_04_05_2022.json
```

### D3FEND Key Links

```
D3FEND Home:       https://d3fend.mitre.org/
D3FEND Matrix:     https://d3fend.mitre.org/
```

### Other Projects

```
Adversary Emulation Library:  https://ctid.mitre.org/resources/adversary-emulation-library/
Caldera:                      https://caldera.mitre.org/
AADAPT:                       https://aadapt.mitre.org/
ATLAS:                        https://atlas.mitre.org/
ATLAS Matrix:                 https://atlas.mitre.org/matrices/ATLAS
CTID:                         https://ctid.mitre.org/
```

### ATT&CK ID Format Reference

| Format | Example | Meaning |
|---|---|---|
| `TXXXX` | T1595 | Technique ID |
| `TXXXX.YYY` | T1595.001 | Sub-technique ID |
| `GXXXX` | G0129 | Group ID |
| `SXXXX` | S0XXX | Software/Tool ID |
| `MXXXX` | M1XXX | Mitigation ID |
| `DSXXXX` | DS0028 | Detection Strategy ID |

### ATT&CK ↔ D3FEND ↔ CAR Relationship

```
ATT&CK Technique (T1053 - Scheduled Task)
    ↓ Detection?
    CAR-2020-09-001 → Splunk query / pseudocode to detect it
    ↓ Defense?
    D3FEND → Credential Rotation, Process Termination, etc.
```

---

## 9. Key Terminology

| Term | Definition |
|---|---|
| **ATT&CK** | MITRE's knowledge base of adversary tactics and techniques based on real-world observations |
| **TTP** | Tactics, Techniques, and Procedures — the complete description of how an adversary operates |
| **Tactic** | The adversary's high-level goal (the *why*) |
| **Technique** | How the adversary achieves a tactic (the *what*) |
| **Sub-technique** | A more specific implementation of a technique |
| **Procedure** | The exact real-world execution of a technique by a specific group |
| **APT** | Advanced Persistent Threat — a sophisticated, long-term adversary (often nation-state) |
| **ATT&CK Navigator** | Web-based tool for annotating and exploring ATT&CK matrices |
| **CAR** | Cyber Analytics Repository — ready-made detection analytics mapped to ATT&CK |
| **Pseudocode** | Human-readable description of detection logic — not tied to a specific tool |
| **SIEM** | Security Information and Event Management — platform that aggregates and analyzes security logs |
| **D3FEND** | MITRE's framework mapping defensive techniques — the counterpart to ATT&CK |
| **D3-CRO** | Credential Rotation — D3FEND technique for preventing reuse of stolen credentials |
| **Caldera** | MITRE's automated adversary emulation platform using ATT&CK |
| **CTID** | Center for Threat-Informed Defense — maintains the Adversary Emulation Library |
| **Emulation Plan** | Step-by-step guide mimicking a specific threat group's attack methodology |
| **AADAPT** | MITRE framework for adversary techniques targeting digital asset/crypto systems |
| **ATLAS** | MITRE framework for adversary techniques targeting AI and ML systems |
| **CTI** | Cyber Threat Intelligence — information about current and potential threats |
| **Detection Strategy ID** | Unique identifier for a specific detection approach in ATT&CK (e.g., DS0028) |
| **Mitigation** | A recommended defensive action to prevent or reduce the impact of a technique |

---

## 📝 My Notes & Questions

> *[ Write your questions, insights, and connections to other concepts here ]*

---

*TryHackMe — Cybersecurity 101 | MITRE ATT&CK & Frameworks*
