# 🛡️ TryHackMe — Cybersecurity 101

## Module: Forensics Fundamentals | Study Summary

> **Keywords:** Digital Forensics · NIST · Evidence Acquisition · Chain of Custody · Write Blocker · Disk Image · Memory Image · FTK Imager · Autopsy · Volatility · EXIF · Metadata · pdfinfo · exiftool

---

## 📚 Table of Contents

1. [What is Digital Forensics](#1-what-is-digital-forensics)
2. [Digital Forensics Methodology — NIST 4 Phases](#2-digital-forensics-methodology--nist-4-phases)
3. [Types of Digital Forensics](#3-types-of-digital-forensics)
4. [Evidence Acquisition](#4-evidence-acquisition)
5. [Windows Forensics — Disk & Memory Images](#5-windows-forensics--disk--memory-images)
6. [Forensics Tools](#6-forensics-tools)
7. [Practical — Metadata Analysis](#7-practical--metadata-analysis)
8. [Quick Reference](#8-quick-reference)

---

## 1. What is Digital Forensics

**Forensics** is the application of methods and procedures to investigate and solve crimes. The branch that investigates cyber crimes is known as **digital forensics**. A **cyber crime** is any criminal activity conducted on or using a digital device.

Digital forensics teams collect evidence from digital devices securely, analyze it inside a forensics lab using specialized tools, and produce findings for use in legal proceedings.

**Real-world example — bank robber case:**
Evidence found across the suspect's laptop, hard drive, mobile, and USB included:
- Digital map of the bank used for planning
- Documents listing entrance/escape routes and security controls
- Media files from previous robberies
- Illegal chat groups and call records on the mobile phone

All of this constituted admissible digital evidence for legal proceedings.

---

## 2. Digital Forensics Methodology — NIST 4 Phases

The **National Institute of Standards and Technology (NIST)** defines a general four-phase process applicable to every digital forensics case:

```
Collection → Examination → Analysis → Reporting
```

| Phase | Description | Key Point |
|---|---|---|
| **1. Collection** | Identify and collect all relevant devices from the crime scene (computers, laptops, cameras, USBs, etc.). | Original data must **not** be tampered with. A proper document of all collected items must be maintained. |
| **2. Examination** | Filter the collected data to extract only what is relevant to the case. | Example: from thousands of media files, only those recorded on a specific date and time are needed. |
| **3. Analysis** | Correlate multiple pieces of evidence to draw conclusions. Extract a **chronological timeline** of activities relevant to the case. | The most critical phase — findings depend on the quality of correlation. |
| **4. Reporting** | Prepare a detailed report covering methodology, findings, and recommendations. | Must include an **executive summary** suited to all receiving parties (law enforcement, management, non-technical stakeholders). |

---

## 3. Types of Digital Forensics

Different categories of evidence require specialized tools and methodologies:

| Type | Focus | Key Evidence |
|---|---|---|
| **Computer Forensics** | Desktop and laptop computers — the most common type. | Files, browsing history, documents, deleted data |
| **Mobile Forensics** | Mobile devices. | Call records, SMS, GPS locations, app data |
| **Network Forensics** | The entire network rather than individual devices. | Network traffic logs, packet captures |
| **Database Forensics** | Dedicated databases that store critical organizational data. | Unauthorized access, data modification, exfiltration |
| **Cloud Forensics** | Data stored on cloud infrastructure. | Often limited evidence — cloud environments can be difficult to investigate |
| **Email Forensics** | Email communications. | Phishing campaigns, fraud, header analysis |

---

## 4. Evidence Acquisition

Evidence acquisition must be performed carefully to ensure the evidence is legally admissible and forensically sound. Three key practices:

### Proper Authorization

The forensics team must obtain **authorization from relevant authorities** before collecting any data. Evidence collected without prior approval may be deemed **inadmissible in court**. This also ensures the investigation operates within legal boundaries given the sensitivity of the data involved.

> 🔗 [Sample Chain of Custody Form — NIST](https://www.nist.gov/document/sample-chain-custody-formdocx)

### Chain of Custody

A **chain of custody** is a formal document that tracks every piece of evidence throughout the investigation. It prevents unauthorized tampering and establishes accountability. Key details recorded:

- Description of the evidence (name, type)
- Names of individuals who collected it
- Date and time of collection
- Storage location
- Access times and names of individuals who accessed it

> 💡 The chain of custody document can be used in court to **prove the integrity and reliability** of the evidence. Without it, no individual can be held accountable if evidence goes missing or is altered.

### Write Blockers

A **write blocker** is a hardware device placed between the suspect's storage media and the forensic workstation during acquisition. It **prevents any write operations** from occurring on the original evidence — including background OS processes that might silently alter file timestamps.

Without a write blocker: background tasks on the forensic workstation can modify timestamps on the evidence drive → incorrect analysis results → potentially inadmissible evidence.

---

## 5. Windows Forensics — Disk & Memory Images

Windows is the most common OS investigated in digital forensics cases. Two types of **forensic images** (bit-by-bit copies) are taken:

| Image Type | What it Contains | Volatility | Priority |
|---|---|---|---|
| **Disk Image** | All data on the storage device (HDD/SSD) — files, documents, browsing history, etc. | **Non-volatile** — survives restart/shutdown | Can be taken at any time |
| **Memory Image** | Data inside RAM — open files, running processes, active network connections, etc. | **Volatile** — **lost on restart or shutdown** | Must be taken **first** before any system changes |

> ⚠️ **Critical:** If a suspect's machine is still running, capture the **memory image first**. Any shutdown or restart permanently destroys all volatile data in RAM.

---

## 6. Forensics Tools

### Disk Acquisition & Analysis

| Tool | Type | Purpose |
|---|---|---|
| **FTK Imager** | GUI — Windows | Creates disk images in various formats. Also used for analyzing disk image contents. Widely used in professional forensics. |
| **Autopsy** | GUI — Open Source | Imports a disk image and performs extensive analysis. Features: keyword search, deleted file recovery, file metadata, extension mismatch detection, and more. |

> 🔗 [Autopsy — Official Site](https://www.autopsy.com/)

### Memory Acquisition & Analysis

| Tool | Type | Purpose |
|---|---|---|
| **DumpIt** | CLI — Windows | Creates memory images from a Windows OS in multiple formats using simple commands. |
| **Volatility** | CLI — Open Source | Analyzes memory images using plugins. Each artifact type has its own plugin. Supports Windows, Linux, macOS, and Android. |

> 🔗 [Volatility Foundation](https://volatilityfoundation.org/)
> 🔗 [DumpIt](https://www.toolwar.com/2014/01/dumpit-memory-dump-tools.html)

---

## 7. Practical — Metadata Analysis

Every digital file carries **metadata** — data about the data. This can reveal critical information during an investigation even without analyzing file contents.

### PDF Metadata — `pdfinfo`

`pdfinfo` displays metadata embedded in PDF files: title, author, creator application, and creation/modification dates.

```bash
# Install if needed (Kali)
sudo apt install poppler-utils

# Read PDF metadata
pdfinfo DOCUMENT.pdf
```

**Example output reveals:**
```
Creator:      Microsoft® Word for Office 365
CreationDate: Wed Oct 10 21:47:53 2018 EEST
Pages:        20
File size:    560362 bytes
PDF version:  1.7
```

> 💡 Exporting a document to PDF preserves most metadata from the original file — the authoring software, creation date, and other details remain embedded.

### Image EXIF Data — `exiftool`

**EXIF (Exchangeable Image File Format)** is a standard for embedding metadata in image files. Images taken with smartphones or digital cameras contain:

- Camera / smartphone model
- Date and time of capture
- Photo settings (focal length, aperture, shutter speed, ISO)
- **GPS coordinates** (latitude and longitude) — when taken with a GPS-enabled device

```bash
# Install if needed (Kali)
sudo apt install libimage-exiftool-perl

# Read all EXIF metadata from an image
exiftool IMAGE.jpg
```

**Example output:**
```
GPS Position: 51 deg 31' 4.00" N, 0 deg 5' 48.30" W
```

To use GPS coordinates in a map search, replace `deg` with `°` and remove extra spaces:
```
51°31'4.00"N 0°5'48.30"W
```

Then search in [Google Maps](https://www.google.com/maps) or [Bing Maps](https://www.bing.com/maps).

---

## 8. Quick Reference

### NIST 4-Phase Flow

```
Collection → Examination → Analysis → Reporting
   ↑              ↑             ↑           ↑
Identify &    Filter data    Correlate   Document
collect all   to relevant    evidence,   findings,
devices       items only     timeline    present
```

### Evidence Acquisition Checklist

```
☐ Obtain proper authorization before touching anything
☐ Document all collected items
☐ Use write blockers when connecting storage media
☐ Capture memory image FIRST (volatile — lost on shutdown)
☐ Capture disk image second
☐ Maintain chain of custody document throughout
```

### Key Terminology

| Term | Definition |
|---|---|
| **Digital Forensics** | Application of methods and procedures to investigate cyber crimes using digital evidence. |
| **NIST** | National Institute of Standards and Technology — defines the 4-phase digital forensics framework. |
| **Chain of Custody** | Formal document tracking all details of evidence handling — who collected it, when, where stored, who accessed it. |
| **Write Blocker** | Hardware device that prevents any write operations on evidence media during acquisition, preserving original state. |
| **Disk Image** | Bit-by-bit copy of all data on a storage device. Non-volatile — survives restarts. |
| **Memory Image** | Bit-by-bit copy of RAM contents. Volatile — permanently lost on shutdown or restart. |
| **EXIF** | Exchangeable Image File Format — metadata standard embedded in image files (camera model, date, GPS, settings). |
| **Metadata** | Data about data — embedded information in files such as author, creation date, GPS location, software used. |
| **FTK Imager** | Windows GUI tool for disk image acquisition and analysis. |
| **Autopsy** | Open-source digital forensics platform for analyzing disk images. |
| **Volatility** | Open-source memory image analysis tool with plugin-based artifact investigation. |
| **pdfinfo** | CLI tool to extract metadata from PDF files. |
| **exiftool** | CLI tool to read and write metadata (including EXIF/GPS) from image and other file types. |

---

## 📝 My Notes & Questions

> *[ Write your questions, insights, and connections to other concepts here ]*

---

*TryHackMe — Cybersecurity 101 | Forensics Fundamentals*
