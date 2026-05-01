# Tool Reference

> Consolidated cheat sheet for every tool used in this playbook. Install commands, basic usage, and links in one place.

---
## Automated Dorking

When you want to run the full dork matrix against a domain automatically:

```bash
git clone https://github.com/IvanGlinkin/Fast-Google-Dorks-Scan
cd Fast-Google-Dorks-Scan
chmod +x FGDS.sh

./FGDS.sh <DOMAIN>
proxychains bash ./FGDS.sh <DOMAIN>
```

---
## Sherlock — Username Enumeration

```bash
git clone https://github.com/sherlock-project/sherlock
cd sherlock
pip3 install -r requirements.txt

python3 sherlock <USERNAME>
python3 sherlock <USERNAME> --output results.txt
python3 sherlock <USERNAME> --tor
python3 sherlock <USERNAME> --site github --site twitter
```

**URL:** https://github.com/sherlock-project/sherlock

---

## Holehe — Silent Email Platform Check

```bash
pip3 install holehe

holehe <EMAIL>
holehe <EMAIL> --only-used
holehe <EMAIL> --output results.csv
```

**URL:** https://github.com/megadose/holehe

---

## theHarvester — Email and Subdomain Harvesting

```bash
theHarvester -d <DOMAIN> -l 500 -b google
theHarvester -d <DOMAIN> -l 500 -b google,bing,linkedin,hunter
theHarvester -d <DOMAIN> -b all -f results.html
```

**URL:** https://github.com/laramies/theHarvester

---

## h8mail — Breach Email Lookup

```bash
pip3 install h8mail

h8mail -t <EMAIL>
h8mail -t targets.txt
h8mail -t <EMAIL> -c h8mail_config.ini
```

**URL:** https://github.com/khast3x/h8mail

---

## Recon-ng — Modular OSINT Framework

```bash
recon-ng
recon-ng -w <WORKSPACE>

[recon-ng] > workspaces create <NAME>
[recon-ng] > marketplace search <TERM>
[recon-ng] > marketplace install <MODULE>
[recon-ng] > modules load <MODULE>
[recon-ng] > options set SOURCE <DOMAIN>
[recon-ng] > run
[recon-ng] > keys add <KEY> <VALUE>
```

**URL:** https://github.com/lanmaster53/recon-ng

---

## Photon — Web Crawler

```bash
git clone https://github.com/s0md3v/Photon
cd Photon
pip3 install -r requirements.txt

python3 photon.py -u https://<DOMAIN> -l 3 -t 100 --wayback
```

**URL:** https://github.com/s0md3v/Photon

---

## Social Analyzer — Multi-Platform Username Analysis

```bash
git clone https://github.com/qeeqbox/social-analyzer
cd social-analyzer
pip3 install -r requirements.txt

python3 app.py --cli --mode "fast" \
  --username "<FIRSTNAME> <LASTNAME>" \
  --websites "youtube facebook instagram twitter" \
  --output "pretty" \
  --options "found,title,link,rate"
```

**URL:** https://github.com/qeeqbox/social-analyzer

---

## GHunt — Google Account OSINT

```bash
git clone https://github.com/mxrch/GHunt
cd GHunt
pip3 install -r requirements.txt

python3 ghunt.py email <EMAIL>
```

**URL:** https://github.com/mxrch/GHunt

---

## GitFive — GitHub User Tracking

```bash
git clone https://github.com/mxrch/GitFive
cd GitFive
pip3 install -r requirements.txt

python3 gitfive.py user <GITHUB_USERNAME>
python3 gitfive.py org <ORG_NAME>
```

**URL:** https://github.com/mxrch/GitFive

---

## linkedin2username — Employee Username Generation

```bash
git clone https://github.com/initstring/linkedin2username
cd linkedin2username
pip3 install -r requirements.txt

python3 linkedin2username.py -u <YOUR_EMAIL> -c "<COMPANY_NAME>"
```

**URL:** https://github.com/initstring/linkedin2username

---

## Osintgram — Instagram OSINT

```bash
git clone https://github.com/Datalux/Osintgram
cd Osintgram
pip3 install -r requirements.txt

python3 main.py <TARGET_USERNAME>
```

**Available commands:**
```
followers    followings    info    photos
comments     tagged        likes   stories
```

**URL:** https://github.com/Datalux/Osintgram

---

## cloud_enum — Multi-Cloud Asset Enumeration

```bash
git clone https://github.com/initstring/cloud_enum
cd cloud_enum
pip3 install -r requirements.txt

python3 cloud_enum.py -k <COMPANY_NAME>
```

**URL:** https://github.com/initstring/cloud_enum

---

## tweets_analyzer — Twitter Activity Analysis

```bash
git clone https://github.com/x0rz/tweets_analyzer
cd tweets_analyzer
pip3 install -r requirements.txt

python3 tweets_analyzer.py -n <USERNAME>
```

**URL:** https://github.com/x0rz/tweets_analyzer

---

## Fast Google Dorks Scan — Automated Dorking

```bash
git clone https://github.com/IvanGlinkin/Fast-Google-Dorks-Scan
cd Fast-Google-Dorks-Scan
chmod +x FGDS.sh

./FGDS.sh <DOMAIN>
proxychains bash ./FGDS.sh <DOMAIN>
```

**URL:** https://github.com/IvanGlinkin/Fast-Google-Dorks-Scan

---

## Web-Based Tools (No Install Required)

| Tool | Purpose | URL |
|------|---------|-----|
| Hunter.io | Email pattern discovery | https://hunter.io |
| DeHashed | Breach monitoring | https://dehashed.com |
| Intelligence X | OSINT search engine | https://intelx.io |
| HaveIBeenPwned | Breach email check | https://haveibeenpwned.com |
| DorkSearch | Faster Google dorking | https://dorksearch.com |
| GHDB | Google Hacking Database | https://www.exploit-db.com/google-hacking-database |
| DNSDumpster | DNS recon and mapping | https://dnsdumpster.com |
| Wayback Machine | Archived page lookup | https://web.archive.org |
| Truecaller | Phone number lookup | https://www.truecaller.com |
| GetContact | Crowdsourced phone names | https://www.getcontact.com |
| NerdyData | Technology stack recon | https://www.nerdydata.com |
| OSINT Recon Tool | OSINT mindmap | https://recontool.org/#mindmap |
| Yandex Images | Reverse image search | https://yandex.com/images |
| PimEyes | Face recognition search | https://pimeyes.com |
| VAHAN | Indian vehicle registration | https://vahan.parivahan.gov.in |
| Zaubacorp | Indian company lookup | https://www.zaubacorp.com |
