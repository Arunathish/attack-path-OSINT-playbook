# WordPress OSINT

**Role in Framework:** Specialized Expansion layer. Applied when Domain OSINT confirms a WordPress installation on an authorized target. Focus is risk reasoning, not exploit execution.

---

## What You Are Assessing

WordPress itself is not the vulnerability. The vulnerability is the gap between what is installed and what is maintained.

```
Is this installation actively maintained?
What plugins and themes are installed?
Are those plugins and themes up to date?
Are there exposed files that reveal credentials or configuration?
```

An outdated WordPress core on a low-traffic site is a different risk than an outdated plugin on a high-traffic e-commerce platform. Context matters.

---

## Step 1 — Identify the Platform

WordPress leaves consistent fingerprints. Read the page source.

**URL indicators:**
```
/wp-content/
/wp-includes/
/wp-admin/
/wp-login.php
```

**Meta tag indicator:**
```html
<meta name="generator" content="WordPress x.x.x" />
```

Some hardened installations remove the generator tag. If the meta tag is missing but `/wp-content/` paths are present, it is still WordPress.

---

## Step 2 — Identify Installed Plugins

Plugins are the primary attack surface. WordPress core vulnerabilities are patched quickly. Plugin vulnerabilities persist for months or years because site owners do not update.

Plugin names appear in page source inside asset paths:
```
/wp-content/plugins/<plugin-name>/
```

**Discovery dork:**
```
site:<DOMAIN> inurl:/wp-content/plugins/
```

---

## Step 3 — Identify Themes

Themes are a secondary attack surface. Premium themes with poor update habits are frequent vulnerability sources.

```
/wp-content/themes/<theme-name>/
```

---

## Step 4 — Version Detection

**readme.html** — WordPress core readme, often left accessible, contains version number:
```
<DOMAIN>/readme.html
<DOMAIN>/wp-admin/readme.html
```

**Feed meta:**
```
<DOMAIN>/?feed=rss2
```
The RSS feed often contains a generator tag with the exact WordPress version.

**Asset versioning:** Script and style URLs sometimes include version query parameters: `?ver=5.9.3`

**Plugin-specific:**
```
/wp-content/plugins/<plugin>/readme.txt
/wp-content/plugins/<plugin>/CHANGELOG.md
```

---

## Step 5 — From Version to Risk Reasoning

```
Plugin identified
     ↓
Search CVE databases for that plugin
  → nvd.nist.gov
  → cve.mitre.org
  → wpscan.com/plugins
     ↓
CVE found → understand the vulnerability type
  → RCE: remote code execution — critical
  → LFI: local file inclusion — read sensitive files
  → SQLi: database access — data exposure
  → Upload: arbitrary file upload — often leads to RCE
  → XSS: cross-site scripting — session hijacking
     ↓
Evaluate actual impact in context
  → Is this an authenticated or unauthenticated vulnerability?
  → What access does exploitation give?
  → Is there a public PoC? (changes likelihood, not just severity)
```

The goal is risk reasoning — not exploit execution. Understanding the vulnerability type tells you the impact. Understanding the context tells you the likelihood.

---

## Exposed WordPress Files — High Value Targets

| File | What it reveals |
|------|----------------|
| `wp-config.php` | Database credentials, secret keys — should never be accessible |
| `wp-config.php.bak` | Backup of config — sometimes left in webroot |
| `.htaccess` | Rewrite rules, access controls |
| `debug.log` | Error log — leaks internal paths, function names, queries |
| `xmlrpc.php` | XML-RPC endpoint — used for brute force and DDoS amplification |

**Dorks for exposed WordPress files:**
```
site:<DOMAIN> inurl:wp-config.php
site:<DOMAIN> inurl:debug.log
site:<DOMAIN> inurl:xmlrpc.php
```

---

## XML-RPC — Specific Attack Surface

`xmlrpc.php` being accessible is itself a finding regardless of exploitation. It enables:

- Credential brute force via multicall method (test 500 passwords in one request)
- Pingback reflection for DDoS
- Content injection if credentials are obtained

If `xmlrpc.php` returns a 200 response and the organization does not use the Jetpack plugin or mobile WordPress app, there is no legitimate reason for it to be enabled.

---

## WPScan — Dedicated WordPress Scanner

When manual recon identifies a WordPress installation on an authorized target, WPScan automates plugin, theme, and version enumeration. Full usage in `17-tool-reference.md`.

---

## Blue Team Hardening

| Exposure | Hardening |
|----------|-----------|
| Version in meta tag | Remove generator tag in `functions.php` |
| readme.html accessible | Delete `readme.html` from webroot |
| Plugin versions in URLs | Use asset versioning obfuscation |
| xmlrpc.php accessible | Disable if not needed, block via WAF |
| wp-config.php | Move above webroot, restrict permissions |
| debug.log accessible | Disable `WP_DEBUG_LOG` in production |
| Outdated plugins | Automated update policy, plugin audit quarterly |

> **Self-audit:** Check your own WordPress installations for readme.html, xmlrpc.php, and debug.log accessibility. These are five-minute fixes that close common attack vectors.
