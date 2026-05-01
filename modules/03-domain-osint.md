# Domain OSINT — Expanding the Attack Surface

**Role in Framework:** Primary Expansion layer (infrastructure mapping). Use this to build the infrastructure graph from a single root domain outward.

---

## The Expansion Model

Start with the root domain and expand outward systematically. Do not skip layers.

```
Root Domain
     ↓
Subdomains          → How large is the attack surface?
     ↓
DNS Records         → What infrastructure is behind it?
     ↓
Tech Stack          → What vulnerabilities might apply?
     ↓
Cloud Assets        → What storage and services are exposed?
     ↓
Public Endpoints    → What entry points exist?
     ↓
Historical Data     → What existed before that still works?
```

A chaotic attack surface — many subdomains, inconsistent naming, mixed cloud providers — usually means weak governance. Weak governance means things get misconfigured and forgotten. That is where findings live.

---

## Layer 1 — Subdomain Enumeration

Subdomains reveal the real size of the attack surface. The main domain is the public face. The subdomains are where development, staging, internal tooling, and forgotten infrastructure lives.

| Subdomain Pattern | What it signals |
|------------------|-----------------| 
| `dev.company.com` | Development environment — often less hardened |
| `staging.company.com` | Pre-production — may contain real data |
| `api.company.com` | API surface — authentication and data endpoints |
| `admin.company.com` | Administrative interface — high value |
| `vpn.company.com` | Remote access — reveals VPN technology |
| `jenkins.company.com` | CI/CD pipeline — often misconfigured |
| `jira.company.com` | Project management — internal tickets |
| `confluence.company.com` | Internal wiki — documentation and credentials |

Are there subdomains that should not be publicly reachable? A `jenkins` or `confluence` subdomain facing the internet is a finding before you even open it.

**Dorks:**
```
site:*.<DOMAIN>
site:<DOMAIN> -www
```

**Tools:**
```bash
theHarvester -d <DOMAIN> -b google,bing,dnsdumpster,virustotal
subfinder -d <DOMAIN>
amass enum -d <DOMAIN>
```

Web: https://dnsdumpster.com

---

## Layer 2 — DNS Record Analysis

DNS records expose infrastructure decisions. Each record type tells a different story.

| Record | What to look for |
|--------|-----------------|
| `A` | IP addresses — pivot to ASN and hosting provider |
| `MX` | Mail provider — Google Workspace, M365, or self-hosted |
| `TXT` | SPF, DKIM, DMARC — email security posture |
| `CNAME` | Third-party services — reveals SaaS stack |
| `SOA` | Zone info — sometimes leaks internal hostnames |

```bash
dig <DOMAIN> ANY
dig <DOMAIN> TXT
dig <DOMAIN> MX
nslookup -type=ANY <DOMAIN>
```

Does DMARC exist? Is it enforced? No DMARC means the domain can be spoofed for phishing — that is a direct social engineering vector, not just a misconfiguration.

---

## Layer 3 — Technology Stack Fingerprinting

Knowing the tech stack tells you what vulnerability classes to consider without running a single exploit.

**Tools:** Wappalyzer (browser extension), builtwith.com, NerdyData

```
CMS: WordPress, Drupal, Joomla    → known CVEs, plugin exposure
Framework: Laravel, Django, Rails → version-specific issues
CDN: Cloudflare, Fastly, Akamai  → WAF detection
Cloud: AWS, GCP, Azure            → informs cloud enumeration
Auth: Okta, Auth0, custom         → SSO attack surface
```

Is the CMS version outdated? Does the framework version have a known unpatched CVE? Tech stack fingerprinting gives you a vulnerability hypothesis to test.

---

## Layer 4 — Cloud Asset Enumeration

Once you know the cloud provider from DNS and tech stack analysis, enumerate the storage surface. Full dork list is in `02-google-dorking.md`.

What naming patterns would this company use for buckets?

```
<companyname>-assets        <companyname>-backup
<companyname>-dev           <companyname>-prod
<companyname>-staging       <companyname>-logs
<companyname>-cdn           <companyname>-uploads
```

Companies follow predictable patterns. Generate the candidate list first, then verify.

---

## Layer 5 — Historical Data

Organizations change. Old infrastructure gets decommissioned but DNS records persist, cached pages remain indexed, and archive services record everything.

```
# All snapshots of a domain
https://web.archive.org/web/*/<DOMAIN>

# Google cache
cache:<DOMAIN>
cache:<SUBDOMAIN>.<DOMAIN>
```

What to look for in archives:
- Old login pages that may still be live
- Previously exposed directories that were closed then re-exposed
- Old employee names and emails from archived team pages
- Deprecated API endpoints that still respond

---

## Attack Path Example — Domain to Internal Access

```
Step 1 — Root domain enumeration
site:*.company.com → surfaces dev.company.com

Step 2 — Dev subdomain investigation
intitle:"index of" site:dev.company.com
→ Open directory at dev.company.com/files/

Step 3 — Sensitive file in directory
.env file visible in listing

Step 4 — Credential extraction
.env contains:
  DB_PASSWORD=Sup3rS3cr3t!
  AWS_ACCESS_KEY_ID=AKIA...
  AWS_SECRET_ACCESS_KEY=...

Step 5 — Lateral pivot
AWS keys → S3 bucket access → internal data
DB credentials → direct database connection
```

Every step here is passive OSINT until the last one. No CVE exploited. The vulnerability was a developer who pushed a `.env` file to a publicly accessible dev server. This is how most real breaches start — not zero-days, but misconfigurations.

---

## Blue Team Hardening

| Finding | Hardening |
|---------|-----------|
| Exposed dev/staging subdomain | Restrict to VPN or internal IP only |
| Open directory listing | Disable at web server level |
| `.env` file accessible | Move secrets to vault, add to `.gitignore` |
| No DMARC enforcement | Implement `p=reject` policy |
| Outdated CMS/framework | Patch management process |
| Public cloud storage | Block public ACL, enable access logging |
