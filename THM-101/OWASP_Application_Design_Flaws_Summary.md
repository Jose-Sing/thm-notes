# 🛡️ TryHackMe — Cybersecurity 101

## Module: OWASP Top 10 — Application Design Flaws | Study Summary

> **Keywords:** OWASP · Security Misconfiguration · Supply Chain · Cryptographic Failures · Insecure Design · AES · Base64 · ECB · API Exposure · curl · CyberChef

---

## 📚 Table of Contents

1. [Module Overview](#1-module-overview)
2. [AS02 — Security Misconfigurations](#2-as02--security-misconfigurations)
3. [AS03 — Software Supply Chain Failures](#3-as03--software-supply-chain-failures)
4. [AS04 — Cryptographic Failures](#4-as04--cryptographic-failures)
5. [AS06 — Insecure Design](#5-as06--insecure-design)
6. [Quick Reference Cheatsheet](#6-quick-reference-cheatsheet)
7. [Key Terminology](#7-key-terminology)
8. [My Notes & Questions](#8-my-notes--questions)

---

## 1. Module Overview

This module covers 4 of the **OWASP Top 10 2025** categories related to **architectural and design flaws** — vulnerabilities that exist not in one line of buggy code, but in how the entire system was designed, configured, or deployed.

| Category | Name                           | Core Problem                                                            |
| -------- | ------------------------------ | ----------------------------------------------------------------------- |
| **AS02** | Security Misconfigurations     | Unsafe defaults, exposed services, misconfigured permissions            |
| **AS03** | Software Supply Chain Failures | Compromised or unverified third-party components                        |
| **AS04** | Cryptographic Failures         | Weak or misused encryption — data exposed that should be private        |
| **AS06** | Insecure Design                | Flawed logic or architecture baked in from the start — can't be patched |

---

## 2. AS02 — Security Misconfigurations

### What it is

Systems deployed with **unsafe defaults, incomplete settings, or exposed services**. These are not code bugs — they are deployment and configuration mistakes that create easy entry points.

### Common Patterns

- Default credentials or weak passwords left unchanged
- Unnecessary services or endpoints exposed to the internet
- Misconfigured cloud storage (S3, Azure Blob, GCP) accessible publicly
- Verbose error messages leaking stack traces or system details
- Outdated software or containers with known vulnerabilities
- Exposed AI/ML endpoints without access controls

### How to Prevent

- Harden defaults — remove unused services and features
- Enforce least privilege across all systems
- Audit cloud permissions regularly
- Keep software and containers patched
- Suppress stack traces in error messages
- Integrate configuration review into the deployment pipeline

> **Real example:** In 2017, Uber exposed a backup AWS S3 bucket with sensitive driver and rider data publicly accessible — no credentials needed. One deployment mistake = major breach.

---

### 🔬 Challenge — AS02 Walkthrough

**Target:** `http://10.67.185.189:5002`

**Flag:** `THM{V3RB0S3_3RR0R_L34K}`

#### What happened

Opening the target in the browser revealed the GET format for interacting with the API. The developers left the API endpoints exposed and unauthenticated.

#### How the flag was obtained

**Method 1 — Browser (GET request):**

```
http://10.67.185.189:5002/api/user/thm
```

**Method 2 — curl (POST request):**

```bash
curl -X POST http://10.67.185.189:5002/api/user/j
```

#### Why this works

The API had no authentication or authorization. Navigating directly to user endpoints returned data (including the flag) without any credentials. The error messages were also verbose — leaking internal details about the API structure, which is what the flag name references: `V3RB0S3_3RR0R_L34K` = **Verbose Error Leak**.

> **Key lesson:** APIs should never expose user data or internal structure without proper authentication. Verbose error messages are a recon gift to attackers.

---

## 3. AS03 — Software Supply Chain Failures

### What it is

Weaknesses that exist **not in your code**, but in the third-party libraries, services, models, or build tools your application depends on. One compromised dependency can compromise the entire system.

### Common Patterns

- Using unverified or unmaintained libraries
- Automatically installing updates without verification
- Insecure CI/CD pipelines that allow build tampering
- Over-reliance on third-party AI models without auditing
- No monitoring for vulnerabilities in dependencies post-deployment

### How to Prevent

- Verify all third-party components and AI models before use
- Lock down CI/CD pipelines
- Sign and verify software updates
- Track provenance and licensing for all dependencies
- Monitor for unusual behavior from dependencies at runtime

> **Real example:** SolarWinds 2021 — attackers inserted malicious code into a trusted software update, affecting thousands of organizations that automatically installed it. The attacker never touched the core code — only the update pipeline.

---

### 🔬 Challenge — AS03 Walkthrough

**Target:** `http://10.67.185.189:5003`

The app imports an old `lib/vulnerable_utils.py` component. The challenge hint says: "Can you **debug** it?"

**Flag:** obtained via terminal

#### Command used

```bash
curl -X POST http://10.67.185.189:5003/api/process \
  -H "Content-Type: application/json" \
  -d '{"data": "debug"}'
```

#### Breaking down the command

| Part                                    | What it does                                                                                            |
| --------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| `curl`                                  | The tool that sends the HTTP request over the network                                                   |
| `-X POST`                               | Defines the HTTP method — POST sends data to the server for processing (vs GET which just requests)     |
| `http://10.67.185.189:5003/api/process` | The exact endpoint (address) on the server                                                              |
| `-H "Content-Type: application/json"`   | Header — tells the server the data coming is formatted as JSON (like a label on a box saying "fragile") |
| `-d '{"data": "debug"}'`                | The actual data payload — sending the word `debug` as the value of the `data` field                     |

#### Why this works

The vulnerable `lib/vulnerable_utils.py` component had a **debug mode** that was never disabled in production. Sending `"data": "debug"` triggered this hidden path in the old library and returned the flag. This is a classic supply chain failure — the vulnerability wasn't in the app's own code, but in a dependency it blindly trusted.

> **Key lesson:** Old, unmaintained dependencies can harbor dormant debug paths, backdoors, or known CVEs. Dependencies must be audited and updated — not just imported and forgotten.

---

## 4. AS04 — Cryptographic Failures

### What it is

Encryption used **incorrectly or not at all**. Includes weak algorithms, hard-coded keys, poor key management, or unencrypted sensitive data. These flaws let attackers access information that should be private.

### Common Patterns

- Weak/deprecated algorithms: MD5, SHA-1, ECB mode
- Hard-coded secrets in source code or config files
- No encryption for sensitive data at rest or in transit
- Poor key rotation or no key management strategy
- Self-signed or invalid TLS certificates

### How to Prevent

- Use modern algorithms: AES-GCM, ChaCha20-Poly1305, TLS 1.3
- Use a key management service (AWS KMS, Azure Key Vault, HashiCorp Vault)
- Rotate secrets and keys regularly
- Never hard-code secrets in source code
- Maintain an inventory of all certificates and keys

---

### 🔬 Challenge — AS04 Walkthrough

**Target:** `http://10.67.185.189:5004`

**Flag:** `THM{CRYPTO_FAILURE_H4RDCOD3D_K3Y}`

#### The full reasoning chain

The challenge presented an encrypted string. Here's how to approach it step by step:

---

**Step 1 — Identify the encoding format**

The string looked like: `Nzd42HZGgUIUlpILZRv0je...`

**Pattern recognition table** (memorize these three — they cover 90% of beginner challenges):

| Format           | How to recognize                                                              | Example            |
| ---------------- | ----------------------------------------------------------------------------- | ------------------ |
| **Base64**       | Letters (A-Z, a-z), numbers, sometimes `+` and `/`. Often ends in `=` or `==` | `SGVsbG8gd29ybGQ=` |
| **Hexadecimal**  | Only 0-9 and A-F. Never a Z or G. Usually in pairs                            | `48 65 6c 6c 6f`   |
| **URL Encoding** | Normal text interrupted by `%` followed by two hex chars                      | `hello%20world`    |

> **If you can't recognize it:** Use CyberChef's **Magic** operation — paste the input, add Magic to the recipe, and it analyzes the pattern and suggests the correct transformation. **Hash Analyzer** is also useful.

**Conclusion:** The string was Base64. First step in CyberChef: `From Base64`.

---

**Step 2 — Read the source code clues (F12)**

Inspecting the page source revealed hardcoded values — this is the vulnerability itself:

| Clue in source code               | Technical meaning                               | CyberChef parameter |
| --------------------------------- | ----------------------------------------------- | ------------------- |
| `SECRET_KEY = "my-secret-key-16"` | The encryption key (password for the lock)      | **Key**             |
| `ENCRYPTION_MODE = "ECB"`         | How data blocks are organized during encryption | **Mode**            |
| `KEY_SIZE = 128`                  | Key is 128 bits (16 chars × 8 bits)             | AES-128             |

> this information was taken by the web page checking with F12

---

**Step 3 — Identify the algorithm**

The code didn't say "AES" explicitly, but: `ECB mode + 128/192/256-bit key = AES` in 99% of cases. AES (Advanced Encryption Standard) is the current gold standard symmetric cipher.

> **Why ECB is dangerous:** ECB (Electronic Codebook) encrypts each block independently. Identical plaintext blocks produce identical ciphertext blocks — this creates patterns that leak information. Modern practice uses AES-GCM or CBC with IV instead.

---

**Step 4 — Decrypt in CyberChef**

```
Recipe:
1. From Base64         (Input: Auto, Output: Raw)
2. AES Decrypt         (Key: my-secret-key-16 | Mode: ECB | Input: Raw | Output: Raw)
```

> **Why Input/Output Raw?** After Base64 decoding, the data becomes raw bytes that the browser can't display as text. Setting `Raw` tells CyberChef to pass the bytes directly to the next step without trying to interpret them as UTF-8 characters.

**Full reasoning summary:**

```
1. What does it look like?  → Base64 → From Base64 (removes the encoding wrapper)
2. What clues are in code?  → Key + Mode + Key Size → AES Decrypt
3. What are the parameters? → Key: my-secret-key-16 | Mode: ECB
4. Configure and bake       → Flag revealed
```

---

## 5. AS06 — Insecure Design

### What it is

Flawed **logic or architecture built into a system from the start** — not a bug that can be patched, but a fundamental design assumption that is wrong. Often stems from skipped threat modeling or no security requirements defined before building.

### Common Patterns (2025)

- Weak business logic: recovery flows, approval processes with no verification
- Flawed assumptions about how users or models will behave
- AI components with unchecked authority or access
- Debug/test bypasses left in production
- No abuse-case review — only happy-path thinking
- LLMs and automation agents with no guardrails

### AI-Era Insecure Designs

| Design Failure                  | What it means                                                              |
| ------------------------------- | -------------------------------------------------------------------------- |
| **Prompt injection**            | User input blended with system prompts — attacker hijacks the AI context   |
| **Blind trust in model output** | Acting on AI decisions without validation — fragile and exploitable        |
| **Poisoned models**             | Models from unverified sources with embedded backdoors or hidden behaviors |

### How to Design Securely

- Treat every model as untrusted until proven otherwise
- Separate system prompts from user content
- Require human review for high-risk AI actions
- Apply least privilege across users, APIs, and AI services
- Build threat modeling into every stage of development — not just at the start
- Continuously monitor for logic flaws and emergent risks as features are added

> **Real example:** Clubhouse's early design assumed users would only interact via the mobile app. The backend API had no authentication — researchers could query user data, room info, and private conversations directly. The design assumption was wrong, and no patch could fix the architecture.

---

### 🔬 Challenge — AS06 Walkthrough

**Target:** `http://10.67.185.189:5005`

**Flag:** `THM{1NS3CUR3_D35IGN_4SSUMPT10N}`

#### What the hint meant

"Have they assumed that only mobile devices can access it?" → The API was designed assuming only an authenticated mobile app would call it. No access controls enforced server-side.

#### How the flag was obtained

**Step 1 — Check if users are exposed:**

```
http://10.67.185.189:5005/api/users
```

This returned a list of users including an `admin` account — no authentication required.

**Step 2 — Access admin messages directly:**

```
http://10.67.185.189:5005/api/messages/admin
```

Returned:

```json
{
  "content": "Admin panel access key: THM{1NS3CUR3_D35IGN_4SSUMPT10N}"
}
```

#### Why this works

The design assumed that only the mobile app (with its built-in flow) would ever call these endpoints. No server-side authentication or authorization was enforced. Any client — including a browser or curl — could call the API directly. This is a design failure, not a code bug. The fix requires rethinking the architecture — not adding a line of validation.

> **Key lesson:** Security cannot be enforced only at the client level. The server must always verify identity and authorization on every request, regardless of which client is sending it.

---

## 6. Quick Reference Cheatsheet

### OWASP Design Flaws — Summary

| Category                       | Root cause             | Key risk              | Can you patch it?         |
| ------------------------------ | ---------------------- | --------------------- | ------------------------- |
| AS02 Security Misconfiguration | Wrong setup/defaults   | Easy attacker entry   | ✅ Yes — fix config        |
| AS03 Supply Chain              | Compromised dependency | Hidden code execution | ✅ Yes — update/verify     |
| AS04 Cryptographic Failure     | Bad/missing encryption | Data exposure         | ✅ Yes — use modern crypto |
| AS06 Insecure Design           | Wrong architecture     | Logic bypass          | ❌ No — must redesign      |

### curl Reference

```bash
# GET request (default)
curl http://IP:PORT/endpoint

# POST with JSON body
curl -X POST http://IP:PORT/endpoint \
  -H "Content-Type: application/json" \
  -d '{"key": "value"}'

# POST with specific data
curl -X POST http://IP:PORT/api/process \
  -H "Content-Type: application/json" \
  -d '{"data": "debug"}'
```

### CyberChef — Decryption Workflow

```
Unknown string → recognize encoding → strip it (From Base64 / From Hex)
                ↓
         Find clues in source code (F12):
         Key? Mode? IV? Algorithm?
                ↓
         Apply decrypt operation (AES Decrypt, XOR, etc.)
         with correct parameters
                ↓
         Set Input/Output to Raw when passing between steps
```

### Encoding Recognition Quick Reference

| Format      | Pattern                               | Tip                               |
| ----------- | ------------------------------------- | --------------------------------- |
| Base64      | A-Z, a-z, 0-9, `+`, `/` — ends in `=` | Most common in web/malware        |
| Hex         | 0-9 and A-F only, pairs               | Never contains G-Z                |
| URL Encoded | `%XX` patterns in URLs                | `%20` = space, `%2F` = `/`        |
| Unknown     | Can't tell                            | Use CyberChef **Magic** operation |

### ECB vs Modern Encryption

| Mode                | Problem                                                  | Use instead      |
| ------------------- | -------------------------------------------------------- | ---------------- |
| **ECB**             | Identical blocks → identical ciphertext → leaks patterns | AES-GCM          |
| **MD5 / SHA-1**     | Broken — collision attacks exist                         | SHA-256 / bcrypt |
| **Hard-coded keys** | Anyone with source access has the key                    | AWS KMS / Vault  |

---

## 7. Key Terminology

| Term                          | Definition                                                                                             |
| ----------------------------- | ------------------------------------------------------------------------------------------------------ |
| **OWASP**                     | Open Web Application Security Project — produces the Top 10 list of critical web security risks        |
| **Security Misconfiguration** | Vulnerability from incorrect setup rather than buggy code                                              |
| **Supply Chain Attack**       | Attack targeting a dependency or tool your code trusts, not your code directly                         |
| **Cryptographic Failure**     | Encryption used incorrectly — weak algorithm, hard-coded key, or no encryption at all                  |
| **Insecure Design**           | Architectural flaw baked in from the start — cannot be fixed with a patch                              |
| **ECB**                       | Electronic Codebook — dangerous AES mode where identical plaintext blocks produce identical ciphertext |
| **AES**                       | Advanced Encryption Standard — current gold standard symmetric cipher                                  |
| **Hard-coded secret**         | A credential or key embedded directly in source code instead of a secret manager                       |
| **Verbose error**             | Error message that leaks internal system details (stack traces, paths, API structure)                  |
| **curl**                      | Command-line tool for sending HTTP requests to servers                                                 |
| **API endpoint**              | A specific URL on a server that performs a defined action when called                                  |
| **Prompt injection**          | Attack where user input manipulates an AI system's behavior by overriding system prompts               |
| **Least privilege**           | Security principle: grant only the minimum permissions needed — nothing more                           |
| **Threat modeling**           | Proactive process of identifying potential attack paths before building a system                       |
| **CI/CD pipeline**            | Continuous Integration/Continuous Deployment — automated process for building and deploying software   |
| **Magic (CyberChef)**         | CyberChef operation that auto-detects encoding/encryption type from input patterns                     |

---

## 📝 My Notes & Questions

> *[ Write your questions, insights, and connections to other concepts here ]*

---

*TryHackMe — Cybersecurity 101 | OWASP Top 10 — Application Design Flaws*
