# Blue Team Detections

> Every OSINT technique has a defensive equivalent. This section maps each red team method to what it looks like from the defender's perspective — log signatures, detection logic, and hardening recommendations.

---

## Detection Philosophy

Most OSINT is passive. It generates no logs on your servers because it never touches them. The attacker queries Google, not your infrastructure.

This means your primary defensive leverage is:

```
1. Reduce exposure surface (less to find)
2. Self-audit periodically (find it before attackers do)
3. Harden the few techniques that do touch your systems
4. Train employees on personal OSINT hygiene
```

---

## Detection Matrix

| Technique | Server-Side Logs? | Detection Method | Hardening |
|-----------|------------------|------------------|-----------|
| Google dorking | No | Google Alerts on your domain | Restrict sensitive files, robots.txt, WAF |
| .git directory access | Yes — if accessed | WAF rule on `/.git/` path | Block public access, return 403 |
| theHarvester scraping | No | N/A | Periodic self-harvest |
| Holehe email check | No (platform-side only) | N/A | Enforce MFA across all employee accounts |
| SMTP verification probe | Yes — mail logs | fail2ban, SMTP rate limiting | Disable VRFY/EXPN commands |
| Sherlock username enum | No | N/A | Audit consistent username usage across employees |
| Truecaller / GetContact | No | N/A | Employee awareness training |
| UPI name lookup | No | N/A | Separate personal and work numbers |
| S3 bucket enumeration | Yes — S3 access logs | CloudTrail alert on 403 from unknown IPs | Block public access, enable access logging |
| GitHub secret scraping | No | N/A | Enable GitHub secret scanning, pre-commit hooks |
| LinkedIn scraping | LinkedIn-side only | N/A | Limit public profile fields, privacy settings |

---

## WAF Rules — .git Directory

Nginx:
```nginx
location ~ /\.git {
    deny all;
    return 403;
}
```

Apache:
```apache
RedirectMatch 404 /\.git
```

Alert rule — if WAF returns 200 on `/.git/HEAD`, trigger immediate incident.

---

## SMTP Hardening

Disable enumeration commands:
```bash
# Postfix — disable VRFY
smtpd_discard_ehlo_keywords = vrfy

# Exim — disable EXPN
disable_EXPN = true
```

fail2ban rule for RCPT TO probing:
```ini
[smtp-probe]
enabled = true
filter = smtp-probe
logpath = /var/log/mail.log
maxretry = 5
bantime = 3600
```

---

## Google Alerts — Passive Self-Monitoring

Set alerts for:
```
"<COMPANY_NAME>" password
"@<DOMAIN>" filetype:pdf
"<DOMAIN>" leak
"<DOMAIN>" breach
```

Free, passive, runs continuously. Alerts you when your org surfaces in indexed paste sites, documents, or news.

---

## Sigma Rules

### .git Directory Probe Detection
```yaml
title: Git Directory Exposure Probe
status: experimental
description: Detects attempts to access exposed .git directories
logsource:
    category: webserver
detection:
    selection:
        cs-uri-stem|contains:
            - '/.git/HEAD'
            - '/.git/config'
            - '/.git/COMMIT_EDITMSG'
    condition: selection
falsepositives:
    - Legitimate internal git tooling
level: high
tags:
    - attack.reconnaissance
    - attack.t1592
```

### Sensitive File Access Detection
```yaml
title: Sensitive File Extension Access
status: experimental
description: Detects access to commonly sensitive file extensions
logsource:
    category: webserver
detection:
    selection:
        cs-uri-stem|endswith:
            - '.env'
            - '.bak'
            - '.sql'
            - '.conf'
            - '.htpasswd'
    condition: selection
level: medium
tags:
    - attack.reconnaissance
    - attack.t1083
```

---

## Employee OSINT Hygiene Checklist

```
[ ] Consistent usernames across platforms — audit and diversify
[ ] LinkedIn profile — limit to job title and company, remove phone/email
[ ] WhatsApp — set profile photo and about to contacts only
[ ] Telegram — enable privacy settings, hide phone number
[ ] Personal vs work number separation for UPI
[ ] GitHub — configure privacy email in Git settings
[ ] Old accounts — deactivate or delete forgotten platform registrations
[ ] Breach check — run personal email through HaveIBeenPwned
```
