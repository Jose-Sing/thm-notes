# 🛡️ TryHackMe — Cybersecurity 101

## Module: Burp Suite — The Basics | Study Summary

> **Keywords:** Burp Suite · Proxy · Intercept · FoxyProxy · Sitemap · Scope · XSS · HTTPS · CA Certificate

---

## 📚 Table of Contents

1. [What is Burp Suite](#1-what-is-burp-suite)
2. [Key Features — Community Edition](#2-key-features--community-edition)
3. [The Dashboard](#3-the-dashboard)
4. [Navigation & Keyboard Shortcuts](#4-navigation--keyboard-shortcuts)
5. [Settings — Global vs Project](#5-settings--global-vs-project)
6. [The Burp Proxy — Core Concepts](#6-the-burp-proxy--core-concepts)
7. [Connecting the Browser — FoxyProxy Setup](#7-connecting-the-browser--foxyproxy-setup)
8. [The Burp Suite Built-in Browser](#8-the-burp-suite-built-in-browser)
9. [Target Tab — Site Map, Scope & Issue Definitions](#9-target-tab--site-map-scope--issue-definitions)
10. [Proxying HTTPS — Installing the CA Certificate](#10-proxying-https--installing-the-ca-certificate)
11. [Example Attack — Bypassing a Client-Side Filter (XSS)](#11-example-attack--bypassing-a-client-side-filter-xss)
12. [Quick Reference](#12-quick-reference)

---

## 1. What is Burp Suite

Burp Suite is a **Java-based framework** designed as a comprehensive solution for web application penetration testing. It is the **industry standard tool** for hands-on security assessments of web and mobile applications, including those that rely on APIs.

At its core, Burp Suite captures and enables manipulation of all **HTTP/HTTPS traffic** between a browser and a web server. By intercepting requests, users can route them to various components within the framework, modify them before they reach the server, and manipulate responses before they reach the browser.

### Editions

| Edition          | Key Characteristics                                                                                                                                         |
| ---------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Community**    | Free for non-commercial use. Limited features but sufficient for learning and manual testing. No project saving.                                            |
| **Professional** | Automated vulnerability scanner, unlimited fuzzer/brute-forcer, project saving, report generation, built-in API, full BApp Store access, Burp Collaborator. |
| **Enterprise**   | Server-based continuous scanning. Periodically scans web apps for vulnerabilities automatically, similar to Nessus for infrastructure.                      |

---

## 2. Key Features — Community Edition

| Tool                        | What it does                                                                                                                                 |
| --------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| **Proxy**                   | Intercepts and modifies HTTP/HTTPS requests and responses between browser and server. The core of Burp Suite.                                |
| **Repeater**                | Captures, modifies, and resends the same request multiple times. Essential for manually crafting payloads (e.g. SQLi) and testing endpoints. |
| **Intruder**                | Sprays endpoints with automated requests. Used for brute-force attacks and fuzzing. Rate-limited in Community edition.                       |
| **Decoder**                 | Decodes captured data or encodes payloads before sending. Supports Base64, URL encoding, hex, and more.                                      |
| **Comparer**                | Compares two pieces of data at word or byte level. Useful for spotting differences between responses.                                        |
| **Sequencer**               | Assesses the randomness of tokens such as session cookies or generated values. Weak randomness can expose devastating attacks.               |
| **Extensions / BApp Store** | Supports extensions written in Java, Python (Jython), or Ruby (JRuby). The BApp Store provides third-party modules like Logger++.            |

---

## 3. The Dashboard

The Burp Dashboard is divided into four quadrants:

- **Tasks** — Defines background tasks. In Community, "Live Passive Crawl" automatically logs visited pages.
- **Event log** — Shows actions performed by Burp Suite and connection details made through Burp.
- **Issue Activity** — *Professional only.* Displays vulnerabilities found by the automated scanner, ranked by severity.
- **Advisory** — Detailed information about identified vulnerabilities including references and suggested remediations.

> 💡 **Tip:** The `?` (question mark) icons throughout Burp open context-specific help windows. Use them freely when exploring unfamiliar sections.

---

## 4. Navigation & Keyboard Shortcuts

Navigation is done through the **top menu bar** (modules) and a **second bar below it** (sub-tabs). Tabs can also be detached into separate windows via `Window > Detach`.

| Shortcut           | Tab       |
| ------------------ | --------- |
| `Ctrl + Shift + D` | Dashboard |
| `Ctrl + Shift + T` | Target    |
| `Ctrl + Shift + P` | Proxy     |
| `Ctrl + Shift + I` | Intruder  |
| `Ctrl + Shift + R` | Repeater  |

---

## 5. Settings — Global vs Project

| Type                       | Scope                                                                                                        |
| -------------------------- | ------------------------------------------------------------------------------------------------------------ |
| **Global (User) Settings** | Affect the entire Burp Suite installation. Persist across sessions.                                          |
| **Project Settings**       | Specific to the current session only. Lost when Burp is closed — Community does not support saving projects. |

Access via the **Settings button** in the top nav bar. Many modules (like Proxy) have a shortcut button that opens settings directly on the relevant section.

---

## 6. The Burp Proxy — Core Concepts

The Burp Proxy sits between the browser and the web server, intercepting all HTTP/HTTPS traffic. It is the most fundamental tool in Burp Suite.

### Intercept ON vs OFF

| Mode              | Behavior                                                                                                                                   |
| ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------ |
| **Intercept ON**  | Burp pauses every request and waits for manual approval before forwarding. Use this when you want to inspect or modify a specific request. |
| **Intercept OFF** | Traffic flows freely. Burp still logs everything in HTTP History and populates the Site Map. This is the **passive listening mode**.       |

> ⚠️ **Common mistake:** Leaving Intercept ON when browsing causes the browser to hang waiting for approval on every single request. Keep it **OFF** unless actively modifying a specific request.

### Notable Proxy Settings

- **Response Interception:** By default, responses are not intercepted. Can be enabled with rules to intercept specific responses.
- **Match and Replace:** Uses regex to dynamically modify incoming/outgoing requests — useful for changing user agents or manipulating cookies.

---

## 7. Connecting the Browser — FoxyProxy Setup

To route browser traffic through Burp, **FoxyProxy** is used as a Firefox extension.

1. Install **FoxyProxy Basic** extension in Firefox *(pre-installed on the TryHackMe AttackBox)*.
2. Open FoxyProxy Options and click **Add** to create a new proxy configuration.
3. Set: **Title:** `Burp` | **Proxy IP:** `127.0.0.1` | **Port:** `8080`
4. Save and activate the `Burp` profile from the FoxyProxy icon in Firefox.
5. Ensure Burp Suite is running — without it, the browser cannot make requests.

> ⚠️ When FoxyProxy is active and Intercept is ON, the browser will hang on every request. Toggle Intercept OFF when only observing traffic passively.

---

## 8. The Burp Suite Built-in Browser

Burp includes a pre-configured **Chromium browser** (accessible via `Proxy > Open Browser`) that routes all traffic through the proxy automatically — no FoxyProxy setup needed.

On Linux running as **root** (like the AttackBox), the browser may fail to start due to sandbox restrictions. Two solutions:

- **Smart option:** Create a low-privilege user and run Burp Suite under that account.
- **Easy option:** `Settings > Tools > Burp's browser` > enable `Allow Burp's browser to run without a sandbox`. ⚠️ Use with caution — a compromised browser grants full machine access.

---

## 9. Target Tab — Site Map, Scope & Issue Definitions

### Sub-tabs Overview

| Sub-tab               | Purpose                                                                                                                                                                                            |
| --------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Site Map**          | Tree-view of all pages and endpoints visited while the proxy is active. Auto-populated by browsing. Captures API endpoints automatically. In Professional, can be populated by automated crawling. |
| **Issue Definitions** | Full list of web vulnerabilities Burp's scanner looks for, with descriptions and references. Available in Community — useful for manual testing reports even without the scanner.                  |
| **Scope Settings**    | Define which domains/IPs are in scope. Controls what gets logged and intercepted. Keeps the traffic view clean and focused.                                                                        |

### Setting Scope

To add a target to scope: `Target tab > right-click the target in the Site Map > Add To Scope`. Burp will ask if out-of-scope traffic should stop being logged — select **Yes**.

To fully restrict the proxy to in-scope traffic only: `Proxy Settings > Intercept Client Requests > enable "And URL Is in target scope"`. This prevents out-of-scope traffic from cluttering the view.

---

## 10. Proxying HTTPS — Installing the CA Certificate

When intercepting HTTPS traffic, the browser throws a certificate error because it does not trust Burp's **PortSwigger CA certificate** by default.

| Step | Action                                                                                            |
| ---- | ------------------------------------------------------------------------------------------------- |
| 1    | With Burp Proxy active, navigate to `http://burp/cert` — downloads the file `cacert.der`.         |
| 2    | In Firefox, go to `about:preferences` and search for "certificates". Click **View Certificates**. |
| 3    | Click **Import** and select the downloaded `cacert.der` file.                                     |
| 4    | Check **"Trust this CA to identify websites"** and click OK.                                      |

Once installed, Burp can intercept and inspect all HTTPS traffic without browser errors.

---

## 11. Example Attack — Bypassing a Client-Side Filter (XSS)

This example demonstrates one of the most important uses of Burp Proxy: **bypassing client-side input validation**.

### The Scenario

A support form has a client-side JavaScript filter that blocks special characters in the email field — preventing XSS payloads from being typed directly into the form.

### Why Client-Side Filters Are Weak

Client-side filters run **in the browser** and can be bypassed by intercepting the request after the browser sends it but before it reaches the server. The server has no filter — only the browser does.

### Attack Walkthrough

1. Enable FoxyProxy and turn **Intercept ON** in Burp.
2. Enter legitimate data in the form (e.g. email: `pentester@example.thm`, query: `Test Attack`) and submit.
3. Burp intercepts the request before it reaches the server.
4. In the intercepted request, replace the email value with the XSS payload:
   
   ```
   <script>alert("Succ3ssful XSS")</script>
   ```
5. Select the payload and URL-encode it with `Ctrl + U` (makes it safe to transmit in HTTP).
6. Click **Forward** to send the modified request to the server.
7. The server receives the payload without any filter — the XSS executes and an alert box appears.

> 💡 **Key insight:** This is **Reflected XSS** — it only affects the user making the request. The vulnerability exists because the server trusted that the browser's client-side filter was sufficient. **Server-side validation is always mandatory.**

---

## 12. Quick Reference

### Core Workflow

| Goal                           | How                                                                               |
| ------------------------------ | --------------------------------------------------------------------------------- |
| Passively observe all traffic  | FoxyProxy ON · Intercept OFF — browse normally, check HTTP History / Site Map     |
| Intercept and modify a request | FoxyProxy ON · Intercept ON — submit action, edit in Burp, click Forward          |
| Populate the Site Map          | Intercept OFF — browse every page of the target manually                          |
| Limit traffic to one target    | Target tab > right-click > Add to Scope > Yes to stop logging out-of-scope        |
| Intercept HTTPS traffic        | Install PortSwigger CA cert via `http://burp/cert` into Firefox certificate store |
| Bypass a client-side filter    | Intercept ON > submit form > modify request in Burp > URL-encode > Forward        |

### Key Terminology

| Term                   | Definition                                                                                      |
| ---------------------- | ----------------------------------------------------------------------------------------------- |
| **Proxy**              | Intermediary that sits between browser and server, capturing all traffic.                       |
| **Intercept**          | Feature that pauses requests/responses for manual review and modification.                      |
| **Site Map**           | Tree of all URLs/endpoints discovered while browsing through Burp.                              |
| **Scope**              | The defined set of targets Burp focuses on — filters noise from unrelated traffic.              |
| **FoxyProxy**          | Firefox extension that routes browser traffic through a proxy (Burp, at `127.0.0.1:8080`).      |
| **CA Certificate**     | PortSwigger's certificate installed in Firefox to allow HTTPS interception without errors.      |
| **Reflected XSS**      | XSS that executes only for the user making the request. Does not persist in the server.         |
| **Client-side filter** | Input validation that runs only in the browser. Trivially bypassed by intercepting the request. |

---

## 📝 My Notes & Questions

> *[ Write your questions, insights, and connections to other concepts here ]*

---

*TryHackMe — Cybersecurity 101 | Burp Suite: The Basics*
