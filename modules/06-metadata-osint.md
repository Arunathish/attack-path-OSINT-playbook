# Metadata Intelligence — Hidden Data Layer

**Role in Framework:** Deep Enrichment layer. Extracts hidden context from files and artifacts to expand identity graphs, reveal infrastructure details, and generate new pivot points that visible content alone cannot provide.

---

## Core Concept

Files contain more than what you can read.

```
Visible content   → what the file says
Metadata          → who made it, when, where, on what system, from what path
```

Metadata is not a finding by itself. It is a pivot generator — every metadata field is a potential new node in the identity graph.

---

## What Metadata Can Reveal

```
Real author names       → even if the document is anonymized
Internal usernames      → exposed in file paths
Organization names      → embedded in software registration
Creation timestamps     → confirms when and correlates with activity
GPS coordinates         → precise physical location from image files
Software and tooling    → reveals internal tech stack
Developer machine paths → exposes infrastructure in binaries
```

---

## Types of Metadata

### 1. Image Metadata (EXIF)

Images captured by cameras and smartphones embed significant metadata by default. Most people are unaware it exists.

**High signal fields:**
```
GPS coordinates     → exact latitude and longitude at time of capture
Device model        → phone or camera model
Timestamp           → date and time of capture (not upload)
Software            → editing tool used (Lightroom, Snapseed, etc.)
```

**Attack path:**
```
Image found on social media or document
→ exiftool image.jpg
→ GPS coordinates present
→ Exact location identified
→ Correlate with target's stated location or activity
→ Pattern of locations across multiple images = behavioral map
```

**Important:** Most social platforms (Instagram, Facebook, Twitter/X) strip EXIF data from images uploaded as media. GPS data from these sources is generally not recoverable. See the Messaging Platforms section below for when metadata survives.

---

### 2. PDF and Document Metadata

PDFs are the highest-frequency metadata source in OSINT. They are published constantly — reports, resumes, proposals, research papers — and rarely stripped before upload.

**High signal fields:**
```
Author          → real name, sometimes different from the document's stated author
Creator         → software used to generate the PDF
Company         → organization name from software registration
FilePath        → internal path of the source file before conversion
Timestamps      → creation and last modification dates
```

**Attack path:**
```
PDF found on Scribd or via Google dork

→ exiftool report.pdf

Output:
  Author:   John Doe
  FilePath: C:\Users\jdoe\Documents\Internal\Q3_Report.pdf
  Creator:  Microsoft Word
  Company:  TargetCorp

→ Username extracted: jdoe
→ Email pattern inferred: jdoe@targetcorp.com
→ Internal tool confirmed: Microsoft Word (not Google Docs)
→ LinkedIn search with name + company → identity confirmed

Outcome:
→ Document → metadata → username → email → identity pivot
```

The file path is often the highest-value field. Internal directory naming conventions, usernames, project names, and department structure are all embedded in a path that the author never considered making public.

---

### 3. Office Files (DOCX, XLSX, PPTX)

Office files are ZIP archives containing XML. Metadata is stored in `docProps/core.xml` and `docProps/app.xml` inside the file.

**High signal fields:**
```
dc:creator          → original author
cp:lastModifiedBy   → last person to edit
cp:revision         → number of revisions (indicates how much internal work went in)
cp:modified         → last modification timestamp
Application         → Office version and platform (Windows/Mac)
Company             → organization from software registration
```

**Extraction:**
```bash
exiftool document.docx

# Or unzip and read directly
unzip -p document.docx docProps/core.xml
unzip -p document.docx docProps/app.xml
```

**Analyst note:** `lastModifiedBy` is often more useful than `creator` — it reveals who handled the document most recently, not just who originally wrote it.

---

### 4. File System Metadata

When files are obtained directly (downloads, authorized access to systems), file system metadata adds temporal context.

```
Created timestamp     → when the file first appeared on this system
Modified timestamp    → last content change
Accessed timestamp    → last time someone opened it
Owner                 → system username of the file owner
```

This is most relevant in authorized red team engagements where file system access is part of scope. It can reveal activity patterns — a file modified at 2am suggests unusual work hours or timezone inconsistency.

---

### 5. Binary and Executable Metadata (Advanced)

Compiled binaries — executables, DLLs, malware samples — embed metadata from the development environment. This is primarily used in malware analysis and threat intelligence, but the extraction methodology is the same.

**High signal fields:**
```
Compile timestamp     → when the binary was built
PDB path              → path to the debug symbols file on the developer's machine
Compiler version      → toolchain used
Embedded strings      → hardcoded paths, URLs, credentials, error messages
```

**Attack path:**
```
Binary sample obtained (authorized malware analysis context)

→ strings malware.exe | grep -i "users\|desktop\|documents\|appdata"

Output:
  C:\Users\dev\Desktop\project\build\malware.exe

→ Developer username: dev
→ Internal project structure visible
→ Machine path pattern identified

→ strings malware.exe | grep -i "http\|ftp\|api\|key\|pass"
→ Hardcoded C2 URL or credential strings extracted
```

PDB paths in particular are a reliable source of developer usernames — they appear because the developer compiled a debug build that was accidentally shipped or leaked.

---

## Tools

### CLI (Primary)

```bash
# Images, PDFs, Office files, and most file types
exiftool <file>
exiftool -all= <file>          # Strip all metadata before publishing

# PDF-specific
pdfinfo <file>

# Strings from binaries and any file
strings <file>
strings <file> | grep -i "users\|path\|password\|http"

# File type identification (never trust the extension)
file <file>

# Binary analysis and embedded file extraction
binwalk <file>
```

### Online Tools

```
exif.tools          → Browser-based EXIF extraction, no install required
metadata2go.com     → Multi-format metadata viewer
virustotal.com      → Binary metadata + threat intelligence (for malware analysis)
```

**Note on tools:** The value is not in running the tool — it is in interpreting the output. A file path means nothing until you extract the username and test it against an email pattern. A GPS coordinate means nothing until you correlate it with claimed location data. Tools surface the data. Analysts generate the pivot.

---

## Metadata in Messaging Platforms

This is the most misunderstood area of metadata OSINT. The behavior is not uniform — and assumptions in either direction will cost you findings.

### What Platforms Generally Do

Modern messaging and social platforms process media on upload. This processing typically includes recompression and metadata stripping.

```
File uploaded as image/video (normal upload)
→ Platform recompresses the file
→ EXIF data stripped or modified
→ Original metadata lost
→ Low value for extraction
```

### The Document Exception

When a file is shared as a document attachment rather than inline media, many platforms skip the recompression step.

```
File shared as document (PDF, ZIP, DOCX, raw image file sent as document)
→ No recompression
→ File transmitted closer to original state
→ Original metadata often preserved
→ High value for extraction
```

**Practical scenario:**
```
Target sends a photo via WhatsApp as a normal image
→ Metadata stripped → GPS unavailable

Target sends the same photo as a document attachment
→ Metadata intact → GPS coordinates present → location extracted
```

**Platform behavior overview (approximate — verify in practice):**

| Platform | Media Upload | Document Upload |
|----------|-------------|-----------------|
| WhatsApp | Strips EXIF | Often preserves metadata |
| Telegram | Strips EXIF | Often preserves metadata |
| Instagram | Strips EXIF | N/A (no document sharing) |
| Signal | Strips EXIF | Varies by version and setting |
| Email | Does not modify | Metadata fully preserved |

**Analyst rule:** Never assume metadata is present. Never assume it is absent. Always verify by running extraction on the actual file received.

```
Always ask:
→ Was this file compressed by the platform?
→ Was it shared as a document or as media?
→ Is this the original file or a processed version?

Metadata availability depends entirely on how the file was transmitted.
```

---

## Analyst Thinking

Metadata should be interrogated, not just read.

```
Is the author name consistent with the claimed identity?
Does the username in the file path match the email pattern you inferred?
Does the timestamp align with the target's stated activity or timezone?
Does the file path reveal internal naming conventions or project structure?
Does the software version reveal an outdated or specific internal toolchain?
Does the GPS location match where the target claims to be?
```

When metadata is inconsistent with other data points:

```
Inconsistency could mean:
→ Alias or pseudonym in use
→ Document was edited by a different person than the author
→ File was created on a shared or corporate machine under a different account
→ Deliberate sanitization attempt (partial strip → metadata is incomplete, not absent)
```

Inconsistency is a finding, not a dead end. It becomes a new hypothesis to test.

---

## Attack Path: Metadata to Identity Pivot (Full Chain)

```
Starting Node: Public document found via Scribd dork

Step 1 — Extract metadata
→ exiftool document.pdf

Step 2 — Parse output
→ Author: Jane Smith
→ Company: AcmeCorp
→ FilePath: C:\Users\jsmith\Documents\Projects\AcmeCorp\Q2_Report.pdf
→ Creator: Microsoft Word 2019

Step 3 — Username extraction
→ Username: jsmith (from file path)

Step 4 — Email pattern inference
→ Confirmed pattern from Hunter.io or domain OSINT: firstname.lastname@acmecorp.com
→ Inferred email: jane.smith@acmecorp.com
→ Or: jsmith@acmecorp.com (test both)

Step 5 — Validation
→ Epieos: Google account check on inferred email
→ LinkedIn search: Jane Smith + AcmeCorp → confirms identity

Step 6 — Expansion
→ Holehe: platform registrations on confirmed email
→ h8mail: breach database check

Outcome:
→ Document → metadata → username → email → confirmed identity → credential intelligence
```

---

## Blue Team

### Strip Metadata Before Publishing

This is the single most effective control. One command removes the attack surface:

```bash
# Remove all metadata from a file
exiftool -all= document.pdf
exiftool -all= image.jpg

# Verify the strip worked
exiftool document.pdf
```

### Audit Regularly

```bash
# Check all PDFs in a directory for author metadata
exiftool -author -company -filepath *.pdf

# Flag files with internal path data
exiftool -r -filepath /path/to/public/documents/
```

### Hardening Table

| Exposure | Defensive Action |
|----------|-----------------|
| Author name in PDF metadata | Strip with `exiftool -all=` before publishing |
| Internal file path in metadata | Strip metadata, or move files to sanitized export directory |
| GPS in images | Disable location tagging on device camera before capture |
| `lastModifiedBy` in Office files | Strip via File → Properties before export |
| Software version revealing outdated tooling | Keep software updated, strip metadata |
| Company name in software registration | Use a generic registration name for tools |

### Employee Awareness

```
[ ] Strip metadata from all files before external publication
[ ] Disable GPS tagging on cameras and smartphones for work-related photos
[ ] Use a dedicated export step for public documents — never publish source files directly
[ ] When sharing files via messaging apps, be aware of document vs. media upload behavior
[ ] Audit the company's public documents quarterly using exiftool batch extraction
```
