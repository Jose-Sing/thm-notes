# 🛡️ TryHackMe — Cybersecurity 101
## Module: OWASP Top 10 — IAAA Failures | Study Summary
> **Keywords:** OWASP · IAAA · Broken Access Control · IDOR · Authentication Failures · Logging Failures · Privilege Escalation · Session Management · Accountability

---

## 📚 Table of Contents

1. [Module Overview](#1-module-overview)
2. [What is IAAA?](#2-what-is-iaaa)
3. [A01 — Broken Access Control](#3-a01--broken-access-control)
4. [A07 — Authentication Failures](#4-a07--authentication-failures)
5. [A09 — Logging & Alerting Failures](#5-a09--logging--alerting-failures)
6. [Quick Reference Cheatsheet](#6-quick-reference-cheatsheet)
7. [Key Terminology](#7-key-terminology)
8. [My Notes & Questions](#8-my-notes--questions)

---

## 1. Module Overview

This module covers 3 of the **OWASP Top 10 2025** categories related to failures in how **Identity, Authentication, Authorization, and Accountability (IAAA)** is implemented in web applications.

| Category | Name | IAAA Component It Breaks |
|---|---|---|
| **A01** | Broken Access Control | Authorization |
| **A07** | Authentication Failures | Authentication |
| **A09** | Logging & Alerting Failures | Accountability |

> **Why these matter:** Weaknesses here let threat actors access other users' data or gain more privileges than they should have. These are not edge cases — A01 Broken Access Control is the **#1 most critical** web security risk in OWASP 2025.

---

## 2. What is IAAA?

IAAA is a framework for thinking about how users and their actions are verified in an application. Each level depends on the previous one — you cannot skip a step.

```
IDENTITY → AUTHENTICATION → AUTHORIZATION → ACCOUNTABILITY
    ↓              ↓               ↓               ↓
  Who are       Prove it       What can         Record
   you?                        you do?         everything
```

| Component | Definition | Example |
|---|---|---|
| **Identity** | The unique account representing a person or service | User ID, email address |
| **Authentication** | Proving that identity is genuine | Password, OTP, passkey, biometrics |
| **Authorization** | What that identity is allowed to do | Role-based permissions, ACLs |
| **Accountability** | Recording and alerting on who did what, when, and from where | Audit logs, SIEM alerts |

> **Key rule:** If a previous step is broken, all subsequent steps are meaningless. If authentication is bypassed, authorization controls become irrelevant.

---

## 3. A01 — Broken Access Control

### What it is

The server **fails to enforce who can access what** on every request. The application trusts the client too much — or doesn't verify at all.

### The Core Attack: IDOR (Insecure Direct Object Reference)

The most common manifestation. If changing an ID in a URL or request lets you access someone else's data, access control is broken.

```
Normal user: GET /api/orders?id=7   → sees their own order ✅
Attack:       GET /api/orders?id=6   → sees another user's order 🚩
```

The server accepted the request without verifying that the authenticated user actually owns record `id=6`.

### Two Types of Privilege Escalation

| Type | What happens | Example |
|---|---|---|
| **Horizontal** | Same role, different user's data | User A accessing User B's account details |
| **Vertical** | Lower role accessing higher-privilege functions | Regular user accessing admin-only actions |

### Common Patterns

- Changing URL parameters, IDs, or object references to access other users' data
- Accessing admin endpoints without admin role
- Bypassing access control by modifying cookies, hidden fields, or JWT tokens
- Accessing resources via direct URL that should require authentication
- Metadata manipulation (modifying CORS headers, JWT claims)
- Missing access control on API endpoints (APIs often assume they won't be called directly)

### How to Prevent

- Enforce access control server-side on every request — never trust the client
- Deny by default: if a permission isn't explicitly granted, it's denied
- Use a centralized access control mechanism — don't reimplement it per feature
- Log access control failures and alert on repeated failures
- Invalidate server-side sessions on logout (don't rely only on client-side token deletion)

---

## 4. A07 — Authentication Failures

### What it is

The application **cannot reliably verify or bind a user's identity**. Attackers exploit this to log in as someone else or maintain a session that should have been revoked.

### Common Patterns

| Failure | What it means |
|---|---|
| **Username enumeration** | Login responses differ for valid vs invalid usernames — lets attackers build a valid user list |
| **Weak/guessable passwords** | No complexity requirements or no lockout after failed attempts |
| **No rate limiting** | Unlimited login attempts → brute-force possible |
| **Logic flaws in login/registration** | Password reset flows that can be manipulated, registration without email verification |
| **Insecure session handling** | Session tokens that don't expire, are predictable, or are transmitted insecurely |
| **Insecure cookie handling** | Cookies missing `HttpOnly`, `Secure`, or `SameSite` flags |

### Specific Attack Scenarios

**Username Enumeration:**
```
POST /login  {"user": "alice", "pass": "wrong"}
→ "Invalid password"         ← confirms alice EXISTS 🚩

POST /login  {"user": "xyz99", "pass": "wrong"}
→ "User not found"           ← reveals xyz99 does NOT exist
```
A correct implementation should return the same generic message for both.

**Session Not Invalidated:**
```
User logs out → session token still valid on the server
Attacker with stolen token can still use it → session fixation / hijacking
```

### How to Prevent

- Return identical, generic error messages for all login failures
- Implement rate limiting and account lockout after failed attempts
- Use MFA (Multi-Factor Authentication) for sensitive accounts
- Set session expiry and invalidate server-side on logout
- Use `HttpOnly` and `Secure` flags on all session cookies
- Don't expose session tokens in URLs
- Use vetted authentication libraries — don't build your own

---

## 5. A09 — Logging & Alerting Failures

### What it is

When applications **don't record or alert on security-relevant events**, defenders have no way to detect or investigate attacks. Logging is the technical foundation of **Accountability** in the IAAA model.

> Without logs, you can't prove who did what, when, or from where. You can't detect attacks in progress. You can't conduct forensic investigations after a breach.

### Common Failures

| Failure | Impact |
|---|---|
| Missing authentication event logs | Can't detect brute-force or credential stuffing |
| Vague or generic error logs | Provides no actionable information for investigation |
| No alerting on brute-force attempts | Attacks run undetected until damage is done |
| No alerting on privilege changes | Privilege escalation goes unnoticed |
| Short log retention | Evidence is gone before an investigation begins |
| Logs stored where attackers can tamper | Evidence integrity cannot be trusted |
| No monitoring for access control failures | IDOR and enumeration attacks are invisible |

### What Good Logging Looks Like

Logs should capture for each security-relevant event:
- **Who** — user ID, IP address
- **What** — action performed
- **When** — timestamp (UTC)
- **Where** — endpoint, resource accessed
- **Outcome** — success or failure

**Events that must always be logged:**
- All authentication attempts (success and failure)
- Password changes and resets
- Access control failures (403 errors)
- Privilege escalation or role changes
- Session creation and termination
- Sensitive data access

### How to Prevent

- Log all authentication, authorization, and sensitive data access events
- Include enough context: user ID, IP, timestamp, resource, outcome
- Set alerts for: repeated failures, privilege changes, unusual access patterns
- Store logs in a tamper-resistant location (separate from the app server)
- Define and enforce a retention policy — at least 90 days active, 1 year archive
- Integrate with a SIEM for centralized correlation and alerting
- Test logging by simulating attacks and verifying they appear in logs

---

## 6. Quick Reference Cheatsheet

### IAAA Chain — What breaks each link

```
IDENTITY         → Spoofing / fake accounts
AUTHENTICATION   → Brute-force, credential stuffing, logic flaws, session hijacking
AUTHORIZATION    → IDOR, privilege escalation (horizontal/vertical), missing controls
ACCOUNTABILITY   → Missing logs, no alerting, tampered evidence, short retention
```

### A01 Attack Patterns

```bash
# IDOR — change the ID to access other users' data
GET /api/orders?id=7   → your data
GET /api/orders?id=6   → someone else's data  🚩

# Vertical privilege escalation — access admin endpoints directly
GET /admin/dashboard   → should return 403, but doesn't  🚩

# Force browsing — access resources by guessing URLs
GET /invoice/2024-001.pdf   → no auth check, file served  🚩
```

### A07 — What to test

```
1. Username enumeration  → Do error messages differ for valid/invalid users?
2. Rate limiting         → Can you make unlimited login attempts?
3. Password policy       → Are weak passwords accepted?
4. Session expiry        → Does the token still work after logout?
5. Cookie flags          → Is HttpOnly and Secure set?
6. Password reset flow   → Can the token be reused? Does it expire?
```

### A09 — Minimum Events to Log

```
✅ Login success and failure (with IP and user)
✅ Logout
✅ Password change/reset
✅ 401 / 403 access control failures
✅ Admin actions
✅ Sensitive data access
✅ Privilege or role changes
✅ Session creation and termination
```

### OWASP IAAA — Summary Table

| Category | Broken IAAA Component | Core Risk | Key Prevention |
|---|---|---|---|
| **A01** Broken Access Control | Authorization | Data from other users / admin access | Server-side enforcement on every request, deny by default |
| **A07** Authentication Failures | Authentication | Login as another user, session hijacking | Rate limiting, MFA, generic error messages, secure session handling |
| **A09** Logging & Alerting | Accountability | Attacks go undetected, no forensic trail | Log all security events, alert on anomalies, tamper-proof storage |

---

## 7. Key Terminology

| Term | Definition |
|---|---|
| **IAAA** | Identity, Authentication, Authorization, Accountability — framework for verifying users and their actions |
| **IDOR** | Insecure Direct Object Reference — changing an ID in a request to access another user's resource |
| **Broken Access Control** | Server fails to enforce who can access what — #1 OWASP 2025 risk |
| **Horizontal Privilege Escalation** | Accessing data of another user with the same role |
| **Vertical Privilege Escalation** | Accessing functions or data reserved for a higher-privilege role |
| **Authentication Failure** | Application cannot reliably verify user identity |
| **Username Enumeration** | Being able to determine if a username is valid based on different server responses |
| **Rate Limiting** | Restricting the number of requests a client can make in a time period — prevents brute-force |
| **Session Fixation** | Attacker sets a known session token before authentication — valid after user logs in |
| **Session Hijacking** | Stealing a valid session token to impersonate an authenticated user |
| **HttpOnly flag** | Cookie attribute preventing JavaScript from accessing the cookie — mitigates XSS session theft |
| **Secure flag** | Cookie attribute ensuring it's only sent over HTTPS |
| **Accountability** | The ability to prove who did what, when, and from where — requires complete logs |
| **SIEM** | Security Information and Event Management — platform for centralized log collection and alerting |
| **Audit log** | A record of security-relevant events used for forensic investigation and compliance |
| **Tamper-resistant logging** | Storing logs in a separate, write-once location that attackers on the app server cannot modify |
| **Deny by default** | Security posture where access is denied unless explicitly granted |
| **Brute-force attack** | Systematically trying all possible passwords or tokens until the correct one is found |

---

## 📝 My Notes & Questions

> *[ Write your questions, insights, and connections to other concepts here ]*

---

*TryHackMe — Cybersecurity 101 | OWASP Top 10 — IAAA Failures*
