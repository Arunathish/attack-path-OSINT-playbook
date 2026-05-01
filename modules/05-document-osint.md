# Document Intelligence — Scribd and Public Repositories

**Role in Framework:** Node Enrichment layer. Documents are high-signal artifacts that deepen identity graphs, reveal internal structure, and generate pretext material for social engineering assessments.

---

## Why Documents Are High Signal

A single leaked resume or internal report can contain more actionable intelligence than hours of social media enumeration. Most analysts overlook document repositories — that is exactly why they are valuable.

When you find a document, interrogate it through the Framework lens:

```
Is this document recent or outdated?
Is the email address in it still active?
Does this reveal internal structure, tooling, or terminology?
Does this name someone in a role that makes them a useful pivot target?
What would an attacker do with this specific document?
```

A resume from 2019 is less valuable than one from last month. An org chart from a director is more valuable than one from an intern. Context determines signal quality.

---

## Scribd

Scribd is one of the largest public document repositories on the internet. Most OSINT guides skip it — less competition means findings are less likely to have been noticed.

**What gets uploaded:**
- Resumes and CVs (email, phone, address, skills, employment history)
- Internal company reports and presentations
- Training materials (reveals internal tooling and processes)
- PDFs with embedded metadata (author name, creation software, internal paths)
- Proposals and RFPs (reveals vendor relationships and budget)

**Dorks:**
```
site:scribd.com "<COMPANY_NAME>"
site:scribd.com "@<DOMAIN>"
site:scribd.com "<PERSON_NAME>" "<COMPANY>"
site:scribd.com "<PHONE_NUMBER>"
site:scribd.com "<DOMAIN>" filetype:pdf
site:scribd.com "<DOMAIN>" resume | cv | report
```

---

## Attack Path: Document to Targeted Attack

```
Step 1 — Scribd dork surfaces a resume
site:scribd.com "CompanyName" resume

Step 2 — Resume contains
Name: John Doe
Email: john.doe@company.com
Role: IT Infrastructure Manager
Skills: Active Directory, Azure AD, Cisco ASA

Step 3 — Role analysis
IT Infrastructure Manager = privileged AD access
Azure AD = cloud identity admin
This is a high-value phishing target

Step 4 — Email confirmed via Holehe
john.doe@company.com → registered on LinkedIn, GitHub

Step 5 — LinkedIn profile
Confirms role, tenure, recent projects
Reveals team members (other targets)

Step 6 — Targeted attack surface
Spear phish crafted using internal terminology from the resume
Pretext: IT infrastructure upgrade, Azure AD migration
```

The resume handed an attacker everything needed to craft a convincing targeted attack — role, skills, internal terminology, and direct email. No active recon required.

---

## Other High-Value Document Repositories

### SlideShare
Presentations often contain org charts, roadmaps, internal metrics, and employee names.

```
site:slideshare.net "<COMPANY_NAME>"
site:slideshare.net "@<DOMAIN>"
site:slideshare.net "<PERSON_NAME>" "<COMPANY>"
```

### Issuu
Digital publications, brochures, and reports.

```
site:issuu.com "<COMPANY_NAME>"
site:issuu.com "<DOMAIN>"
```

### Google Drive and Docs
Shared files set to "anyone with the link" get indexed by Google. Internal reports, spreadsheets, and presentations surface here regularly.

```
site:drive.google.com "<DOMAIN>"
site:docs.google.com "<COMPANY_NAME>"
site:drive.google.com "<COMPANY_NAME>" budget | report | internal
```

### Wayback Machine
Deleted documents leave traces. Pages that were public before a security review may still be archived.

```
site:web.archive.org "<DOMAIN>"
https://web.archive.org/web/*/<DOMAIN>/*.pdf
```

---

## PDF Metadata — The Hidden Layer

PDFs carry metadata that is invisible when you read the document but extractable with tools. This metadata often reveals:

- Author's real name (even if the document is anonymized)
- Software used to create it (reveals internal tooling)
- Internal file paths (reveals directory structure naming conventions)
- Creation and modification dates
- Organization name from software registration

```bash
# Extract PDF metadata
exiftool document.pdf

# What to look for in output
Author:          John Doe
Creator:         Microsoft Word
FilePath:        C:\Users\jdoe\Documents\Internal\Q3_Report.pdf
```

The file path alone confirms the username format (`jdoe`) used internally — which feeds directly into email pattern inference and the identity graph.

---

## Analyst Thinking Checklist for Every Document Found

```
[ ] Who created this? (metadata author vs. document author)
[ ] When was it created? Is it recent enough to be relevant?
[ ] What role does the person hold? Is this a high-value pivot target?
[ ] Are email addresses or phone numbers present?
[ ] Does this reveal internal structure, teams, or reporting lines?
[ ] Does this reveal internal tooling or systems?
[ ] Is the terminology specific enough to use in a social engineering pretext?
[ ] Does this document link to other documents or sources?
```

---

## Blue Team Application

| Exposure Vector | Defensive Action |
|----------------|-----------------|
| Employee resumes on Scribd | Awareness training — employees should not upload resumes with work details to public platforms |
| Internal reports on SlideShare | Content review policy before any document is uploaded externally |
| Google Drive links indexed | Audit sharing settings quarterly, avoid "anyone with the link" for sensitive files |
| PDF metadata exposure | Strip metadata before publishing — `exiftool -all= document.pdf` |
| Archived sensitive documents | Wayback Machine exclusion via `robots.txt`, submit removal requests for sensitive cached content |

> **Self-audit:** Run Scribd and SlideShare dorks against your own organization name and domain. Every document that surfaces is a finding. Prioritize by recency and sensitivity of the content.
