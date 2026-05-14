# 🛡️ TryHackMe — Cybersecurity 101
## Module: CyberChef — The Basics | Study Summary
> **Keywords:** CyberChef · Base64 · URL Encode/Decode · ROT13 · Extractors · UNIX Timestamp · Data Format · Recipe · Operations · Encoding · Decoding

---

## 📚 Table of Contents

1. [What is CyberChef?](#1-what-is-cyberchef)
2. [Accessing the Tool](#2-accessing-the-tool)
3. [Interface — The Four Areas](#3-interface--the-four-areas)
4. [Thought Process — How to Approach CyberChef](#4-thought-process--how-to-approach-cyberchef)
5. [Operations Categories & Key Operations](#5-operations-categories--key-operations)
6. [Deep Dive — How Base64 Works](#6-deep-dive--how-base64-works)
7. [URL Encoding Reference](#7-url-encoding-reference)
8. [Quick Reference Cheatsheet](#8-quick-reference-cheatsheet)
9. [Key Terminology](#9-key-terminology)
10. [My Notes & Questions](#10-my-notes--questions)

---

## 1. What is CyberChef?

CyberChef is a web-based tool developed by **GCHQ** (UK's intelligence agency) for performing data transformations, encoding, decoding, encryption, and extraction operations — all through a simple drag-and-drop interface. No programming knowledge required.

> In other words: it's a Swiss Army knife for data manipulation. You paste in gibberish, chain a few operations, and get readable output. Invaluable in CTFs, malware analysis, and incident response.

**Common use cases in cybersecurity:**
- Decoding Base64-encoded malware strings
- Deobfuscating URLs or scripts
- Extracting IPs, emails, and URLs from log dumps
- Converting timestamps in forensic investigations
- Chaining multiple operations to reverse complex encoding

---

## 2. Accessing the Tool

| Method | How |
|---|---|
| **Online** | [https://gchq.github.io/CyberChef](https://gchq.github.io/CyberChef) — just a browser, no install |
| **Offline / Local** | Download from [GitHub Releases](https://github.com/gchq/CyberChef/releases) — works on Windows and Linux. Use the most stable release. |

> For security-sensitive work (e.g., analyzing real malware samples), use the **offline version** — you don't want to paste sensitive data into an online tool.

---

## 3. Interface — The Four Areas

![CyberChef's main page showing all four areas: Operations, Recipe, Input, and Output](https://tryhackme-images.s3.amazonaws.com/user-uploads/6645aa8c024f7893371eb7ac/room-content/6645aa8c024f7893371eb7ac-1728731934241.png)

| Area | Role |
|---|---|
| **Operations** | Library of all available transformations — searchable and categorized |
| **Recipe** | The "pipeline" — where you drag, arrange, and configure operations |
| **Input** | Where you paste or upload the data you want to process |
| **Output** | Where the result appears after processing |

---

### Operations Area

The complete repository of everything CyberChef can do, organized by category. You can hover over any operation to see a description and a sample before using it.

![Hovering over an operation in CyberChef shows a preview description and link to Wikipedia](https://tryhackme-images.s3.amazonaws.com/user-uploads/6645aa8c024f7893371eb7ac/room-content/6645aa8c024f7893371eb7ac-1729081368672.png)

---

### Recipe Area

The heart of the tool. You drag operations here, arrange them in order, and configure their arguments.

![The Recipe area showing Save, Load, and Clear recipe buttons plus the BAKE button](https://tryhackme-images.s3.amazonaws.com/user-uploads/6645aa8c024f7893371eb7ac/room-content/6645aa8c024f7893371eb7ac-1728731934220.png)

| Feature | Description |
|---|---|
| `Save Recipe` | Save your current operation chain for reuse |
| `Load Recipe` | Load a previously saved recipe |
| `Clear Recipe` | Remove all operations from the current recipe |
| `BAKE!` | Execute the recipe against the input |
| `Auto Bake` | Automatically re-processes every time you change the recipe or input — no need to click BAKE manually |

---

### Input Area

Where you feed data into CyberChef — paste text, drag files, or upload a folder.

![The Input area showing options to add tabs, open files, open folders, and clear input](https://tryhackme-images.s3.amazonaws.com/user-uploads/6645aa8c024f7893371eb7ac/room-content/6645aa8c024f7893371eb7ac-1729081714973.png)

| Feature | Description |
|---|---|
| `Add new input tab` | Open a second input tab to work with different data simultaneously |
| `Open file as input` | Upload a file directly as input |
| `Open folder as input` | Upload an entire folder as input |
| `Clear input and output` | Wipe both areas clean |
| `Reset pane layout` | Restore the default window layout |

---

### Output Area

Displays the result of the applied recipe.

![The Output area showing save, copy, and replace input options](https://tryhackme-images.s3.amazonaws.com/user-uploads/6645aa8c024f7893371eb7ac/room-content/6645aa8c024f7893371eb7ac-1729081715061.png)

| Feature | Description |
|---|---|
| `Save output to file` | Saves result as a `.dat` file |
| `Copy raw output to clipboard` | Copies result directly to clipboard |
| `Replace input with output` | Overwrites the input with the current output — useful for chaining manual steps |
| `Maximise output pane` | Expands the output view |

---

## 4. Thought Process — How to Approach CyberChef

Before touching the tool, have a mental framework. CyberChef is only as useful as your ability to know what you're trying to achieve.

![The thought process diagram: Set objective → Input data → Select operations → Check output → repeat if needed](https://tryhackme-images.s3.amazonaws.com/user-uploads/5e6bbe59a46ee9407fd65bbe/room-content/5e6bbe59a46ee9407fd65bbe-1726735685403.png)

**Four-step workflow:**

```
1. SET OBJECTIVE    → What do I want to achieve?
                       "I found a gibberish string — what does it say?"

2. INPUT DATA       → Paste or upload the data into the Input area.

3. SELECT OPERATIONS → Research what type of encoding/encryption might be involved.
                        Try operations from relevant categories (e.g., Encoding, Encryption).

4. CHECK OUTPUT     → Did I get a readable/expected result?
                        YES → done. NO → go back to step 1 and try different operations.
```

![Detailed thought process diagram with a real example showing a gibberish string being decoded through the four steps](https://tryhackme-images.s3.amazonaws.com/user-uploads/5f9c7574e201fe31dad228fc/room-content/5f9c7574e201fe31dad228fc-1729242272295.png)

> **Key insight:** You often won't know the right operation immediately. Start with common ones (Base64, ROT13, URL Decode) and work from there. CyberChef lets you chain and experiment quickly.

---

## 5. Operations Categories & Key Operations

### Common Operations — Quick Reference

| Operation | What it does | Example |
|---|---|---|
| `From Morse Code` | Translates Morse Code to alphanumeric text | `- .... .-. . .- - ...` → `THREATS` |
| `URL Encode` | Encodes special characters to percent-encoding | `https://tryhackme.com` → `https%3A%2F%2Ftryhackme%2Ecom` |
| `To Base64` | Encodes raw data to ASCII Base64 string | `This is fun!` → `VGhpcyBpcyBmdW4h` |
| `To Hex` | Converts string to hex bytes | `This` → `54 68 69 73` |
| `To Decimal` | Converts string to decimal (ordinal) values | `A` → `65` |
| `ROT13` | Caesar cipher — rotates alphabet by 13 (default) | `Digital Forensics` → `Qvtvgny Sberafvpf` |

---

### Extractors Category

Pulls specific data types out of large blocks of text — extremely useful for analyzing logs or raw network captures.

| Operation | What it extracts |
|---|---|
| `Extract IP addresses` | All IPv4 and IPv6 addresses from the input |
| `Extract URLs` | All URLs (protocol required — HTTP, FTP, etc.) |
| `Extract email addresses` | All strings matching `anything@domain.com` format |

> **Real use case:** Paste a malware log dump → Extract IPs → immediately have a list of C2 server candidates.

---

### Date / Time Category

| Operation | What it does | Example |
|---|---|---|
| `From UNIX Timestamp` | Converts UNIX timestamp → human-readable datetime | `1725654622` → `Fri Sep 6 20:30:22 +04 2024` |
| `To UNIX Timestamp` | Converts datetime string → UNIX timestamp | `Fri Sep 6 20:30:22 +04 2024` → `1725654622` |

> **What is a UNIX Timestamp?** A 32-bit integer representing the number of seconds since **January 1, 1970 UTC** (the UNIX epoch). Commonly seen in logs, forensic artifacts, and malware configs.

---

### Data Format Category — Base Encodings

| Operation | What it does | Example |
|---|---|---|
| `From Base64` | Decodes ASCII Base64 → raw format | `V2VsY29tZSB0byB0cnloYWNrbWUh` → `Welcome to tryhackme!` |
| `URL Decode` | Converts percent-encoded chars → raw values | `https%3A%2F%2Fgchq%2Egithub%2Eio` → `https://gchq.github.io` |
| `From Base85` | Decodes Base85 encoding | `BOu!rD]j7BEbo7` → `hello world` |
| `From Base58` | Decodes Base58 (removes ambiguous chars like l, I, 0, O) | `AXLU7qR` → `Thm58` |
| `To Base62` | Encodes to Base62 (shorter than hex or decimal) | `Thm62` → `6NiRkOY` |

**Base encoding comparison:**

| Encoding | Character Set | Common Use |
|---|---|---|
| Base64 | A-Z, a-z, 0-9, +, / | Most common — email attachments, web tokens, malware obfuscation |
| Base85 | Larger set | More efficient than Base64 — PDF and some protocols |
| Base58 | Excludes l, I, 0, O | Bitcoin addresses — optimized for human readability |
| Base62 | A-Z, a-z, 0-9 | URL shorteners, compact identifiers |

---

## 6. Deep Dive — How Base64 Works

Understanding Base64 manually helps you recognize it instantly and understand what you're decoding.

**The process: Text → Binary → 6-bit groups → Base64 characters**

### Step-by-step example: encoding `THM`

**Step 1 — Convert each character to binary (8 bits each):**

| Character | ASCII Decimal | Binary |
|---|---|---|
| T | 84 | `01010100` |
| H | 72 | `01001000` |
| M | 77 | `01001101` |

Concatenated: `010101000100100001001101` (24 bits total)

**Step 2 — Split into 6-bit groups and convert to decimal:**

| 6-bit group | Decimal |
|---|---|
| `010101` | 21 |
| `000100` | 4 |
| `100001` | 33 |
| `001101` | 13 |

**Step 3 — Look up each decimal in the Base64 index table:**

| Decimal | Base64 Character |
|---|---|
| 21 | V |
| 4 | E |
| 33 | h |
| 13 | N |

**Result: `THM` → `VEhN`** in Base64.

> **Why 6-bit groups?** Base64 uses 64 characters (2⁶ = 64), so 6 bits are needed to represent each character in the output.

> **Recognition tip:** Base64 strings often end with `=` or `==` (padding). They use only letters, numbers, `+`, and `/`. If you see a long string with those characteristics → try `From Base64` first.

---

## 7. URL Encoding Reference

URL encoding (percent-encoding) replaces characters that aren't safe in URLs with `%XX` where XX is the hex value of the character in UTF-8.

| Character | URL Encoded |
|---|---|
| `:` | `%3A` |
| `/` | `%2F` |
| `.` | `%2E` |
| `=` | `%3D` |
| `#` | `%23` |
| `@` | `%40` |
| ` ` (space) | `%20` or `+` |

> **HTML5 default charset is UTF-8.** Full reference: [Wikipedia — Percent-encoding](https://en.wikipedia.org/wiki/Percent-encoding)

---

## 8. Quick Reference Cheatsheet

### CyberChef Links

```
Online:  https://gchq.github.io/CyberChef
Offline: https://github.com/gchq/CyberChef/releases
```

### First operations to try when you don't know the encoding

```
1. From Base64      → Long alphanumeric string ending in = or ==
2. URL Decode       → String with %XX patterns
3. ROT13            → Shifted alphabet gibberish
4. From Hex         → Space-separated hex values (e.g. 54 68 6d)
5. From Morse Code  → Dots and dashes
```

### Common Operation Categories

| Category | When to use |
|---|---|
| **Encoding / Decoding** | Base64, URL, ROT13, Hex — most common for CTFs and malware analysis |
| **Extractors** | Pull IPs, URLs, or emails from large text dumps |
| **Date / Time** | Convert UNIX timestamps in logs and forensic artifacts |
| **Data Format** | Base58, Base85, Base62 — less common encodings |
| **Encryption / Encoding** | When dealing with obfuscated strings of unknown type |

### Recipe tips

```
- Chain operations top to bottom: output of one feeds into the next
- Use Auto Bake to see results instantly as you add operations
- Save recipes you use often — don't rebuild from scratch every time
- Use multiple input tabs to compare results across different samples
```

---

## 9. Key Terminology

| Term | Definition |
|---|---|
| **CyberChef** | GCHQ's web-based tool for data transformation, encoding, decoding, and extraction |
| **Recipe** | A saved sequence of operations applied to input data |
| **Operation** | A single data transformation step (e.g., From Base64, Extract IPs) |
| **BAKE** | The action of executing the recipe against the input |
| **Auto Bake** | Automatic re-processing whenever input or recipe changes |
| **Base64** | Encoding scheme that represents binary data as ASCII text using 64 characters |
| **Base Encoding** | Any encoding scheme that converts binary data into a text representation using a fixed character set |
| **URL Encoding** | Percent-encoding — replaces special URL characters with `%XX` hex values |
| **ROT13** | Caesar cipher rotating letters by 13 positions — applying it twice returns the original text |
| **UNIX Timestamp** | Integer representing seconds elapsed since January 1, 1970 UTC |
| **Extractor** | CyberChef operation category for pulling specific data types (IPs, URLs, emails) from text |
| **Percent-encoding** | Same as URL encoding — special characters replaced with their UTF-8 hex values prefixed by `%` |
| **GCHQ** | UK Government Communications Headquarters — the agency that developed and open-sourced CyberChef |

---

## 📝 My Notes & Questions

> *[ Write your questions, insights, and connections to other concepts here ]*

---

*TryHackMe — Cybersecurity 101 | CyberChef — The Basics*
