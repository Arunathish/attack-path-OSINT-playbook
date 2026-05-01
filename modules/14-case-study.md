# Case Study — Responsible Disclosure: Educational Institution Infrastructure

> Real-world passive recon exercise conducted against a university's public-facing infrastructure. All findings reported responsibly to the institution before public disclosure. No active exploitation was performed at any stage.

---

## Scope and Authorization

- **Target type:** Educational institution (anonymized)
- **Method:** Passive OSINT and Google dorking only
- **Authorization:** Conducted as part of academic security research with responsible disclosure intent
- **Legal framework:** India IT Act 2000, DPDP Act 2023
- **Exploitation:** None — findings documented and reported only

---

## Methodology Used

```
Phase 1 — Google dorking against institution domain
Phase 2 — Passive enumeration of publicly indexed resources
Phase 3 — Manual verification of each finding
Phase 4 — MITRE ATT&CK mapping
Phase 5 — Risk scoring per finding
Phase 6 — Responsible disclosure to institution
Phase 7 — Documentation for public write-up
```

---

## Findings Summary

### Finding 1 — Exposed .git Directory

**Severity:** Critical

**Discovery dork:**
```
intext:"index of /.git" site:<INSTITUTION_DOMAIN>
"(intext:\"index of /.git\") (\"parent directory\")"
```

**What was exposed:**
- Full commit history of web application source code
- Internal file paths and directory structure
- Developer usernames and email addresses from commit metadata
- Potentially sensitive configuration references in commit messages

**MITRE ATT&CK Mapping:**
- T1592.002 — Gather Victim Host Information: Software
- T1083 — File and Directory Discovery

**Risk:** An attacker with commit history can reconstruct the application's logic, identify deprecated but still-running endpoints, find hardcoded credentials in old commits, and map the internal architecture without ever sending an exploit.

**Remediation:**
```nginx
# Block .git directory at web server level
location ~ /\.git {
    deny all;
    return 403;
}
```

---

### Finding 2 — Unauthenticated Administrative Endpoints

**Severity:** High

**Discovery dork:**
```
site:<INSTITUTION_DOMAIN> inurl:admin | inurl:panel | inurl:manage
site:<INSTITUTION_DOMAIN> intitle:"admin" -login
```

**What was exposed:**
- Administrative interface accessible without authentication
- Internal management panel indexed by Google

**MITRE ATT&CK Mapping:**
- T1190 — Exploit Public-Facing Application
- T1078 — Valid Accounts (precursor — panel accessible without credentials)

**Risk:** Unauthenticated admin panels represent direct access to privileged functionality. Google indexing means the surface is discoverable by anyone with a browser.

**Remediation:**
- Restrict admin panel access to internal IP ranges only
- Implement authentication before any admin endpoint is reachable
- Add `Disallow: /admin` to robots.txt (note: this hides, not protects — authentication is the fix)

---

### Finding 3 — Sensitive Data Indexed by Search Engines

**Severity:** High

**Discovery dork:**
```
site:<INSTITUTION_DOMAIN> filetype:pdf | filetype:xls | filetype:csv
site:<INSTITUTION_DOMAIN> ext:log | ext:conf | ext:env
```

**What was exposed:**
- Internal documents publicly indexed
- Structured data files accessible without authentication

**MITRE ATT&CK Mapping:**
- T1530 — Data from Cloud Storage Object
- T1213 — Data from Information Repositories

**Risk:** Indexed sensitive documents provide an attacker with organizational data, personnel information, or configuration details that can be used to craft targeted attacks.

**Remediation:**
- Audit all publicly accessible directories
- Implement proper access controls on document storage
- Add sensitive directories to robots.txt and enforce server-side access controls
- Periodically run self-dorking to catch newly exposed files

---

## Risk Summary Table

| Finding | Severity | CVSS (Approximate) | MITRE Technique | Status |
|---------|----------|-------------------|-----------------|--------|
| Exposed .git directory | Critical | 9.1 | T1592.002, T1083 | Reported |
| Unauthenticated admin endpoint | High | 8.2 | T1190, T1078 | Reported |
| Indexed sensitive data | High | 7.5 | T1530, T1213 | Reported |

---

## Blue Team Detection Angle

This is the key differentiator of this write-up. The same dorks an attacker uses to find these exposures can be run defensively by the organization's own security team.

**Self-audit dork set for educational institutions:**
```
site:<DOMAIN> intext:"index of /.git"
site:<DOMAIN> ext:env | ext:log | ext:sql | ext:conf | ext:bak
site:<DOMAIN> inurl:admin | inurl:panel | inurl:manage
site:<DOMAIN> filetype:pdf | filetype:xls | filetype:csv
```

Running this quarterly and remediating findings before attackers discover them is the practical output of this research.

**Sigma rule for .git probe detection:**
```yaml
title: Git Directory Probe
logsource:
    category: webserver
detection:
    selection:
        cs-uri-stem|contains: '/.git/'
    condition: selection
level: high
```

---

## Disclosure Timeline

| Date | Action |
|------|--------|
| Discovery | Passive recon identified findings |
| T+1 day | Findings documented with evidence |
| T+2 days | Responsible disclosure report submitted to institution |
| T+30 days | Public write-up released (post-disclosure window) |

---

## Key Takeaway

Every finding in this case study was discovered using Google search operators and a browser. No exploit framework, no scanning tool, no authentication bypass. The attack surface was created by misconfiguration and lack of periodic self-audit — not by a sophisticated attacker.

The defensive lesson is straightforward: run these dorks against your own infrastructure before someone else does.
