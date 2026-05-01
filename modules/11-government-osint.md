# Government Site OSINT

**Role in Framework:** Institutional Expansion layer. Applies the same Domain OSINT methodology to government and educational domains, which frequently carry worse security posture due to underfunding and decentralized IT management.

---

## Legal Position

Accessing publicly indexed government files via Google is legal. You are querying a search engine, not accessing a restricted system.

```
Legal:   Finding and reading publicly accessible indexed files
Legal:   Documenting exposure for research purposes
Legal:   Responsible disclosure when sensitive data is found

Not legal: Bypassing authentication
Not legal: Downloading and distributing sensitive data
Not legal: Using findings to cause harm
```

India's IT Act 2000 Section 43 covers unauthorized access. Publicly indexed files accessed via Google do not constitute unauthorized access.

---

## Indian Government Domains

### Surface Mapping
```
site:*.gov.in
site:*.nic.in
site:*.gov.in -www
```

### Document Exposure
```
site:*.gov.in filetype:pdf
site:*.gov.in filetype:xls | filetype:xlsx
site:*.gov.in filetype:doc | filetype:docx
```

### Open Directory Hunting
```
site:*.gov.in intitle:"index of" ext:pdf
site:*.gov.in intitle:"index of" "parent directory"
site:*.gov.in intitle:"index of" ext:log
```

### Election Bodies
```
site:elections.*.gov.in intitle:"index of" "pdf"
site:eci.gov.in filetype:pdf
```

### Sensitive File Types
```
site:*.gov.in ext:env | ext:log | ext:sql | ext:bak | ext:conf
```

---

## US Government Domains

### Surface Mapping
```
site:*.gov
site:*.mil
```

### Document Exposure
```
site:*.gov filetype:pdf
site:*.gov filetype:xls
site:nasa.gov intitle:"index of" ext:pdf
```

### Open Directory Hunting
```
site:*.gov intitle:"index of" "parent directory"
site:*.gov intitle:"index of" ext:log
```

---

## Education Domains

University infrastructure is frequently exposed due to decentralized IT management — each department runs its own servers with varying security standards.

```
site:.edu intitle:"index of"
site:.edu inurl:robots.txt
site:.edu ext:env | ext:log | ext:sql | ext:bak
site:.edu filetype:pdf "internal" | "confidential"
site:.ac.in intitle:"index of"
site:*.ac.in ext:env | ext:log
```

---

## Attack Path Example

```
Step 1 — Surface mapping
site:*.gov.in intitle:"index of" ext:pdf
→ Open directory found on a state government subdomain

Step 2 — Directory contents
PDF files visible: citizen records, employee lists, internal memos

Step 3 — Analyst decision point
Do not download or distribute
Document the URL, screenshot the directory listing
Prepare a responsible disclosure report

Step 4 — Responsible disclosure
Contact the organization's CERT or IT security contact
India CERT: cert-in.org.in
Report the exposure with evidence and remediation recommendation
```

---

## Blue Team Hardening

| Finding | Hardening |
|---------|-----------|
| Open directory | Disable directory listing at web server level |
| Publicly indexed PDFs | Access controls, robots.txt for sensitive paths |
| Exposed config files | Block `.env`, `.conf`, `.log` at WAF level |
| Sensitive data in documents | Document classification and access control review |

> **India CERT contact for responsible disclosure:** https://www.cert-in.org.in
