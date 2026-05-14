# 🛡️ TryHackMe — Cybersecurity 101
## Module: Security Principles | Study Summary
> **Keywords:** CIA · DAD · Bell-LaPadula · Biba · Clark-Wilson · Defence-in-Depth · Zero Trust · ISO/IEC 19249 · Vulnerability · Threat · Risk

---

## 📚 Table of Contents

1. [The CIA Triad](#1-the-cia-triad)
2. [Beyond CIA — Authenticity, Nonrepudiation & Parkerian Hexad](#2-beyond-cia--authenticity-nonrepudiation--parkerian-hexad)
3. [The DAD Triad — The Attacker's Perspective](#3-the-dad-triad--the-attackers-perspective)
4. [Security Models](#4-security-models)
5. [Defence-in-Depth](#5-defence-in-depth)
6. [ISO/IEC 19249 Principles](#6-isoiec-19249-principles)
7. [Zero Trust vs Trust but Verify](#7-zero-trust-vs-trust-but-verify)
8. [Vulnerability vs Threat vs Risk](#8-vulnerability-vs-threat-vs-risk)
9. [Quick Reference Cheatsheet](#9-quick-reference-cheatsheet)
10. [My Notes & Questions](#10-my-notes--questions)

---

## 1. The CIA Triad

The foundation of any security evaluation. Every security decision maps back to one or more of these three properties.

![CIA triangle showing Confidentiality, Integrity, and Availability](https://tryhackme-images.s3.amazonaws.com/user-uploads/5f04259cf9bf5b57aed2c476/room-content/58a85d2e5942ebf3e78eb01c82f563be.png)

| Property | Definition | Broken when... |
|---|---|---|
| **Confidentiality** | Only authorized parties can access data | Unauthorized user reads a file they shouldn't |
| **Integrity** | Data cannot be altered without detection | An attacker modifies a record undetected |
| **Availability** | Systems and data are accessible when needed | A DDoS attack takes down a service |

> **Analogy:** Online shopping — Confidentiality protects your credit card number, Integrity ensures your shipping address isn't changed, Availability ensures the store website is up when you want to buy.

> ⚠️ **Balance matters:** Maximizing one can weaken another. Extreme confidentiality/integrity controls can reduce availability. Extreme availability can expose confidentiality and integrity gaps.

---

## 2. Beyond CIA — Authenticity, Nonrepudiation & Parkerian Hexad

### Authenticity & Nonrepudiation

| Concept | Definition | Example |
|---|---|---|
| **Authenticity** | Data/document genuinely comes from the claimed source — not forged | Verifying that a purchase order really came from a specific customer |
| **Nonrepudiation** | The source cannot deny having created or sent the data | A customer cannot claim they never placed an order once it's cryptographically signed |

> Both are critical in banking, healthcare, and e-commerce where accountability is legally required.

---

### Parkerian Hexad (Donn Parker, 1998)

Extends CIA with two additional elements:

| Element | Definition |
|---|---|
| **Availability** | *(Same as CIA)* |
| **Confidentiality** | *(Same as CIA)* |
| **Integrity** | *(Same as CIA)* |
| **Authenticity** | Data is from the claimed source |
| **Utility** | Data is in a usable form — having encrypted data with a lost key means zero utility even if it's available |
| **Possession** | You have physical/logical control of the data — ransomware removes possession even if the data isn't destroyed |

---

## 3. The DAD Triad — The Attacker's Perspective

Every attack targets one or more CIA properties. DAD is the mirror image.

![DAD triangle showing Disclosure, Alteration, and Destruction](https://tryhackme-images.s3.amazonaws.com/user-uploads/5f04259cf9bf5b57aed2c476/room-content/1742c9b384adb60b896481b2416d2f8a.png)

| Attack | Targets | Example |
|---|---|---|
| **Disclosure** | Confidentiality | Stealing medical records and posting them online |
| **Alteration** | Integrity | Modifying a patient's medication dosage in a database |
| **Destruction / Denial** | Availability | Taking down both primary and backup power to shut a network down |

---

## 4. Security Models

Formal models that define how to enforce CIA in a system.

### Bell-LaPadula — Focuses on CONFIDENTIALITY

**Core rule: "Write Up, Read Down"**

| Rule | Name | What it says |
|---|---|---|
| Simple Security Property | **No Read Up** | A lower-clearance subject cannot read higher-clearance data |
| Star Security Property | **No Write Down** | A higher-clearance subject cannot write to lower-clearance objects |
| Discretionary-Security Property | Access Matrix | Defines specific read/write permissions per subject/object |

> **Limitation:** Not designed to handle file sharing between users.

---

### Biba Model — Focuses on INTEGRITY

**Core rule: "Read Up, Write Down"** *(opposite of Bell-LaPadula)*

| Rule | Name | What it says |
|---|---|---|
| Simple Integrity Property | **No Read Down** | A higher-integrity subject should not read lower-integrity data |
| Star Integrity Property | **No Write Up** | A lower-integrity subject should not write to higher-integrity objects |

> **Limitation:** Does not handle insider threats.

---

### Clark-Wilson Model — Focuses on INTEGRITY

Uses a more structured, transactional approach:

| Concept | Acronym | Definition |
|---|---|---|
| Constrained Data Item | **CDI** | Data whose integrity must be preserved |
| Unconstrained Data Item | **UDI** | All other data (user input, system input) |
| Transformation Procedures | **TP** | Programmed operations (read/write) that maintain CDI integrity |
| Integrity Verification Procedures | **IVP** | Procedures that check and validate CDIs |

---

### Models Side-by-Side

| Model | Goal | Key Rule |
|---|---|---|
| Bell-LaPadula | Confidentiality | Write Up, Read Down |
| Biba | Integrity | Read Up, Write Down |
| Clark-Wilson | Integrity | Controlled access through TPs and IVPs |

---

## 5. Defence-in-Depth

Also called **Multi-Level Security** — the idea that security should never depend on a single control.

![Blueprint showing multiple locked doors and security cameras representing layered security](https://tryhackme-images.s3.amazonaws.com/user-uploads/5f04259cf9bf5b57aed2c476/room-content/d015a7748c8b6930e0c25b571230d873.png)

> **Analogy:** Don't rely only on a locked drawer to protect valuables. Add a locked room, a locked building door, a gate, and cameras. Each layer stops or slows different attackers.

In cybersecurity this translates to: firewall → IDS → endpoint protection → MFA → monitoring → backups. If one layer fails, others still hold.

---

## 6. ISO/IEC 19249 Principles

**ISO/IEC 19249:2017** — Catalogue of architectural and design principles for secure products and systems.

### Five Architectural Principles

| # | Principle | Core Idea |
|---|---|---|
| 1 | **Domain Separation** | Group related components into domains with common security attributes. Example: OS kernel in ring 0, user apps in ring 3 |
| 2 | **Layering** | Structure systems into abstract layers — security policies can be enforced at each layer (e.g., OSI model). Relates to Defence-in-Depth |
| 3 | **Encapsulation** | Hide low-level implementations and provide controlled access via APIs or methods — prevents invalid data states |
| 4 | **Redundancy** | Ensure availability and integrity through duplication (e.g., RAID 5, dual power supplies) |
| 5 | **Virtualization** | Share hardware across multiple OS instances — provides sandboxing, security boundaries, and safe malware detonation |

### Five Design Principles

| # | Principle | Core Idea |
|---|---|---|
| 1 | **Least Privilege** | Grant only the minimum permissions needed to perform a task — nothing more |
| 2 | **Attack Surface Minimization** | Disable unused services, close unused ports, remove unnecessary features |
| 3 | **Centralized Parameter Validation** | Validate all input (especially user input) in one central library — prevents injection and DoS |
| 4 | **Centralized General Security Services** | Centralize authentication, logging, and other security functions — with redundancy to avoid single point of failure |
| 5 | **Preparing for Error and Exception Handling** | Design systems to **fail safe** — a crashed firewall should block all traffic, not allow all traffic. Error messages should never leak sensitive data |

---

## 7. Zero Trust vs Trust but Verify

Two contrasting security philosophies about how much to trust users, devices, and systems.

| Approach | Philosophy | How it works |
|---|---|---|
| **Trust but Verify** | We trust, but we check | Allow access, but log everything and verify via automated tools (proxy, IDS, IPS). Practical but can miss insider threats |
| **Zero Trust** | Trust is a vulnerability | Never trust, always verify. Every entity is adversarial until proven otherwise. Requires authentication + authorization before accessing *any* resource |

**Zero Trust key concepts:**
- Does not grant trust based on network location or device ownership
- **Microsegmentation:** network segments can be as small as a single host — communication between segments requires authentication and ACL checks
- If a breach occurs, damage is more contained due to isolated segments

> **Trade-off:** Perfect zero trust would make a business unworkable. Apply it as far as feasibly possible without breaking operations.

---

## 8. Vulnerability vs Threat vs Risk

Three related but distinct terms — commonly confused.

| Term | Definition | Analogy |
|---|---|---|
| **Vulnerability** | A weakness in a system | A showroom with standard glass walls |
| **Threat** | A potential danger that could exploit the vulnerability | The possibility that someone could break the glass |
| **Risk** | Likelihood of the threat being exploited × business impact | How likely is a break-in, and what would it cost the business? |

> **Real example:** A hospital uses a database that has a known exploit with published proof-of-concept code. Vulnerability = the database bug. Threat = attackers who can use the PoC. Risk = likelihood of attack × impact on patient records and operations.

---

## 9. Quick Reference Cheatsheet

### CIA vs DAD

```
CIA (Defender's goal)    ←→    DAD (Attacker's action)
Confidentiality          ←→    Disclosure
Integrity                ←→    Alteration
Availability             ←→    Destruction / Denial
```

### Security Models Memory Aid

```
Bell-LaPadula  →  CONFIDENTIALITY  →  "Write Up, Read Down"
Biba           →  INTEGRITY        →  "Read Up, Write Down"   (opposite of Bell-LaPadula)
Clark-Wilson   →  INTEGRITY        →  CDI + UDI + TP + IVP
```

### ISO/IEC 19249 Quick Reference

```
ARCHITECTURAL (5):           DESIGN (5):
1. Domain Separation         1. Least Privilege
2. Layering                  2. Attack Surface Minimization
3. Encapsulation             3. Centralized Parameter Validation
4. Redundancy                4. Centralized General Security Services
5. Virtualization            5. Error and Exception Handling (Fail Safe)
```

### Trust Models

```
Trust but Verify  →  Trust + Log + Audit + Automated detection
Zero Trust        →  Never trust + Always verify + Microsegmentation
```

### Vulnerability → Threat → Risk

```
Vulnerability = Weakness
Threat        = Potential danger exploiting that weakness
Risk          = Likelihood × Impact
```

---

## 📝 My Notes & Questions

> *[ Write your questions, insights, and connections to other concepts here ]*

---

*TryHackMe — Cybersecurity 101 | Security Principles*
