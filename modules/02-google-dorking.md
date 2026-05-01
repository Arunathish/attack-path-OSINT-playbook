# Google Dorking

**Role in Framework:** Entry Point and Data Exposure phases. Primary tool for initial node discovery and surface mapping.

---

## Core Operators

| Operator | Function | Example |
|----------|----------|---------| 
| `site:` | Restrict to domain | `site:target.com` |
| `inurl:` | Match string in URL | `inurl:admin` |
| `intitle:` | Match string in title | `intitle:"index of"` |
| `intext:` | Match string in body | `intext:"api_key"` |
| `ext:` / `filetype:` | Match file extension | `ext:env` |
| `cache:` | Cached version | `cache:target.com` |
| `"quotes"` | Exact phrase | `"internal use only"` |
| `\|` | OR | `ext:pdf \| ext:docx` |
| `-` | Exclude | `site:target.com -www` |
| `*` | Wildcard | `site:*.target.com` |

---

## Workflow

### Step 1 — Confirm the Target Surface

Before hunting for secrets, confirm what is publicly reachable.

```
site:<DOMAIN>
site:<DOMAIN> -www
site:*.<DOMAIN>
```

This tells you how many pages are indexed, what subdomains appear, and gives you a rough size of the target's public footprint.

### Step 2 — Hunt for Open Directories

Open directories are misconfigured web servers that list files like a file manager. Everything inside is public.

```
intitle:"index of" site:<DOMAIN>
intitle:"index of /.git" site:<DOMAIN>
intitle:"index of" "parent directory" site:<DOMAIN>
intitle:"index of" ext:log site:<DOMAIN>
intitle:"index of" ext:sql site:<DOMAIN>
intitle:"index of" ".env" site:<DOMAIN>
```

If you find an open directory, classify before you act (see Signal vs Noise in the Analyst Framework). A `/logs` directory is high value. A `/static` directory is low.

### Step 3 — Hunt for Sensitive File Types

```
ext:log | ext:txt | ext:conf | ext:cnf | ext:ini | ext:env | ext:sh | ext:bak | ext:backup | ext:swp | ext:old | ext:git | ext:svn | ext:htpasswd | ext:htaccess site:<DOMAIN>
```

Individual high-value targets:

```
site:<DOMAIN> ext:env
site:<DOMAIN> ext:log
site:<DOMAIN> ext:sql
site:<DOMAIN> ext:bak
site:<DOMAIN> ext:conf
site:<DOMAIN> filetype:pem
site:<DOMAIN> filetype:key
```

### Step 4 — Hunt for Credentials and Secrets

```
site:<DOMAIN> intext:"password"
site:<DOMAIN> intext:"api_key" | intext:"api key" | intext:"apikey"
site:<DOMAIN> intext:"secret_key"
site:<DOMAIN> intext:"AWS_ACCESS_KEY"
site:<DOMAIN> intext:"BEGIN RSA PRIVATE KEY"
site:<DOMAIN> intext:"token" ext:json
site:<DOMAIN> intext:"db_password"
site:<DOMAIN> intext:"mysql_password"
```

### Step 5 — Hunt for Admin and Login Panels

```
site:<DOMAIN> inurl:login | inurl:admin | inurl:portal | inurl:dashboard
site:<DOMAIN> intitle:"admin panel"
site:<DOMAIN> inurl:wp-admin
site:<DOMAIN> inurl:phpmyadmin
site:<DOMAIN> inurl:cpanel
```

### Step 6 — Hunt for Employee and Document Exposure

```
site:<DOMAIN> inurl:team | inurl:staff | inurl:about | inurl:people
"@<DOMAIN>" filetype:pdf
"@<DOMAIN>" filetype:xls | filetype:csv
site:<DOMAIN> filetype:pdf | filetype:docx | filetype:xlsx
```

### Step 7 — Hunt for Code and Paste Leaks

```
site:github.com "<DOMAIN>" password | secret | key | token
site:github.com "<DOMAIN>" filename:.env
site:github.com "<DOMAIN>" filename:config
site:pastebin.com "<DOMAIN>"
site:jsfiddle.net | site:codebeautify.org | site:codepen.io | site:pastebin.com "<DOMAIN>"
site:jsfiddle.net | site:codebeautify.org | site:codepen.io | site:pastebin.com "<DOMAIN>" "demo" "test" "api"
```

---

## Cloud Storage Enumeration

Developers spin up storage buckets during development, set them to public for convenience, and forget to lock them down. Bucket names are often predictable because they follow company naming conventions.

Generate a candidate list first:
```
<companyname>-assets       <companyname>-backup
<companyname>-dev          <companyname>-prod
<companyname>-logs         <companyname>-data
dev.<companyname>          cdn.<companyname>
```

Then use dorks to confirm.

### AWS S3
```
site:s3.amazonaws.com "<DOMAIN>"
site:*.s3.amazonaws.com "<DOMAIN>"
```

### Azure Blob Storage
```
site:blob.core.windows.net "<DOMAIN>"
```

### Google Cloud Storage
```
site:storage.googleapis.com "<DOMAIN>"
site:*.storage.googleapis.com "<DOMAIN>"
```

### Google Drive and Docs
```
site:drive.google.com "<DOMAIN>"
site:docs.google.com "<DOMAIN>"
```

### Firebase Storage
```
site:firebasestorage.googleapis.com "<DOMAIN>"
```

### DigitalOcean Spaces
```
site:digitaloceanspaces.com "<DOMAIN>"
```

### Advanced Cloud Dorks — Secrets Inside Buckets

Once you find a bucket, pivot to hunting what is inside.

```
"api_key" site:s3.amazonaws.com
"secret" site:blob.core.windows.net
"password" site:storage.googleapis.com
site:s3.amazonaws.com "<DOMAIN>" "api_key"
```

### Correlation Chain — The Advanced Move

```
GitHub repo → config file → bucket name hardcoded
       ↓
Direct URL access → confirm if public
       ↓
List bucket contents if ACL allows
       ↓
Pivot to credentials or data found inside
```

---

## Vulnerability-Specific Dorks

### Open Redirects
```
inurl:page= | inurl:url= | inurl:return= | inurl:next= | inurl:redir= | inurl:redirect= | inurl:target= inurl:& inurl:http site:<DOMAIN>
```

### SSRF Candidates
```
inurl:http | inurl:proxy= | inurl:html= | inurl:data= | inurl:resource= inurl:& site:<DOMAIN>
```

### PHP Parameter Exposure
```
ext:php inurl:? site:<DOMAIN>
```

### Subdomain Enumeration
```
site:*.*.*.<DOMAIN>
site:<DOMAIN> -www
```

---

## Platform Discovery via Dorking

Google indexes public content from social platforms. Dorking is often more effective than native platform search for initial discovery — use it to find traces, then hand off to the dedicated OSINT layer for expansion.

### Telegram

Telegram channels and groups are indexed at `t.me`.

```
site:t.me "<KEYWORD>"
site:t.me "<COMPANY_NAME>"
site:t.me "<DOMAIN>"
```

**Purpose:** Identify channels, groups, or mentions related to a target organization or topic. Discovery only — do not stop here. Hand off to the Telegram OSINT section for community mapping, invite pivoting, and deeper analysis.

### Social Media (Identity Pivoting)

```
site:instagram.com "<USERNAME>"
site:instagram.com "<NAME>" "<COMPANY>"
```

Instagram dorking surfaces public profiles, tagged posts, and external sites embedding Instagram content. Use it to confirm whether a username or identity exists on Instagram before pivoting into the full Instagram layer inside Individual OSINT.

**Relationship:**
```
Discovery    → Google Dorking
Expansion    → Telegram OSINT / Individual OSINT (Instagram Pivot)
```

See those sections for community mapping, identity confirmation, and behavioral analysis.

---

## Blue Team Detection

| Attack Path | Server-Side Logs? | Detection | Hardening |
|-------------|------------------|-----------|-----------| 
| `.git` directory access | Yes | WAF rule on `/.git/` path, alert on 200 response | Block with nginx/apache rule |
| Admin panel probing | Yes | Rate limit + alert on 4xx flood | IP allowlist on admin paths |
| Sensitive file access | Yes | WAF rule on `.env`, `.bak`, `.sql` | Block public access, return 403 |
| Cloud bucket enumeration | Yes — provider access logs | CloudTrail/Azure Monitor alert on 403 volume | Block public ACL, enable access logging |
| Paste and code site leaks | No | Google Alerts on your domain | Periodic self-dorking |
| GitHub secret leaks | No | GitHub secret scanning alerts | Pre-commit hooks, `.gitignore` for sensitive files |

> **Self-audit:** Run the full dork set against your own domain every quarter. Document findings. Track exposure reduction over time. This is Attack Surface Management in practice.
