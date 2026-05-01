# Individual OSINT — Building Identity Graphs

**Role in Framework:** Correlation and identity graph building. Use this layer to resolve fragments into a confirmed identity and build a credential intelligence picture.

---

## The Identity Graph Model

Every person has identifiers. Each identifier is a node. The connections are edges. Analysis is building the graph — not collecting isolated facts.

```
Phone Number
     |
UPI → Legal Name (bank-verified)
     |
LinkedIn → Employer → Email Pattern
     |
Corporate Email → Platform Registrations → Breach Data
     |
Username → Cross-Platform Accounts
     |
Profile Photo → Older Accounts → Deeper Personal Data
```

One weak starting point can build to a complete picture if you follow the connections.

---

## Weak Signal Chaining

You rarely get clean data. You get fragments. The core skill is cross-referencing fragments until only one person fits.

```
Fragments:
- First name only
- General city
- One inactive LinkedIn profile

Alone: insufficient.

Chain:
Name + City → LinkedIn → profile photo
Photo → Yandex reverse search → Facebook account
Facebook → full name confirmed → email pattern generated
Email → platform registrations → GitHub found
GitHub → commit metadata → corporate email confirmed
```

Each fragment eliminates candidates. The graph builds itself.

---

## Attack Path: Phone Number to Full Profile

```
Phone number
  → Dorks first (see Phone Number Methods below)
  → Truecaller: crowdsourced name and carrier
  → UPI transfer attempt: bank-verified legal name, no trace

Legal name
  → LinkedIn: employer, title, education, location
  → Google dork: "<TARGET_NAME>" "<EMPLOYER>" filetype:pdf → resume

Employer
  → Email pattern via Hunter.io
  → theHarvester on domain → email confirmed

Corporate email
  → Epieos: Google account pivot, linked services, registered name
  → Holehe: silent platform registration check
  → h8mail: breach database hits

Breach data
  → Password reuse check on corporate systems
  → Username from breach → Sherlock across 300+ platforms

Username
  → Old forum and gaming accounts
  → Profile photo cross-reference → Yandex → older accounts
```

---

## Attack Path: Email to Credential Intelligence

```
Email address
  → Epieos: Google account confirmation, linked services, name
  → Holehe: which platforms is this email registered on?
  → h8mail: which breaches does this email appear in?

Breach data
  → Password patterns from old breaches
  → Username variants used in breach records

GitHub (if registered there)
  → Source code, commit history, secondary emails

Credential intelligence (authorized red team only)
  → Known password variants → VPN, webmail, SSO
```

---

## Attack Path: Profile Photo to Identity Confirmation

```
Profile photo (from any public platform)
  → Yandex Images: best coverage for South Asian profiles
  → Google Images: broad coverage
  → TinEye: exact match

Older account surfaced
  → Facebook: real name, hometown, family connections
  → Old forum: username

Username
  → Sherlock: all platforms using that handle
  → Social Analyzer: activity timing and patterns

Family connections
  → LinkedIn: corroborates target's background
  → JustDial listing: home address, alternate phone
```

---

## End-to-End Identity Correlation Chain

The three attack paths above are starting-node specific. This chain shows what a complete investigation looks like when you run them together — from a single phone number to a confirmed attack surface.

```
Attack Path: Phone Number → Full Identity Graph

Starting Node: Phone Number

Step 1 — Initial Lookup
→ Truecaller / GetContact
→ Extract: name, possible email hints, tags

Step 2 — Identity Confirmation
→ UPI lookup → bank-verified legal name
→ Compare with Truecaller name
→ Mismatch here = shared number or alias — note it, do not discard

Step 3 — Cross-Platform Presence Validation
→ WhatsApp: add to contacts, view profile photo + display name + about text
→ Telegram: open t.me/<number> — extract username, bio, linked channels
→ No message required for either. No notification sent.
→ Profile photo feeds directly into reverse image search (Step 6)

Step 4 — Email Pivot
→ If email available or inferred from name + company pattern:
→ Epieos → Google account confirmation
→ Extract: profile photo, registered name, Google Maps reviews

Step 5 — Behavioral Intelligence
→ Google Maps reviews from Epieos output
→ Identify: frequently visited locations, activity patterns, language usage
→ Recent reviews = account is active and tied to this identity

Step 6 — Professional Identity
→ LinkedIn search using confirmed name + location
→ Extract: employer, role, education, tenure
→ Cross-reference with Telegram bio and WhatsApp about text for consistency

Step 7 — Document Intelligence Pivot
→ Scribd / Google dorks using confirmed name + employer
→ "<NAME>" "<COMPANY>" filetype:pdf
→ Extract: resumes, internal docs, email address patterns

Step 8 — Organization Pivot
→ Company domain confirmed from LinkedIn / documents
→ Email pattern inference → theHarvester for enumeration
→ Employee list begins building

Outcome:
→ Confirmed identity + role + organization + inferred email + potential attack surface
```

### Decision Points

After each step, apply the Framework's decision logic before continuing:

```
Do the names match across Truecaller, UPI, LinkedIn, and documents?
Is the email verified (Epieos confirmed) or inferred (pattern only)?
Are the Google Maps reviews recent enough to confirm active use?
Does LinkedIn data align with what the documents reveal?
Are profile photos consistent across WhatsApp, Telegram, and LinkedIn?

Consistency across three or more sources → high confidence, go deeper
Single-source finding → medium confidence, seek corroboration
Mismatch between sources → false positive risk or shared identifiers — pivot or discard
```

---

## Instagram Pivot — Identity and Behavioral Layer

Instagram belongs in the identity graph when a username, name, or profile photo has been found on another platform. It provides visual confirmation, behavioral patterns, and social connections that other platforms do not expose.

```
Starting Point: Username or name identified from another layer

→ Discovery
   site:instagram.com "<USERNAME>"
   site:instagram.com "<NAME>" "<COMPANY>"

→ Extract from public profile
   - Profile photo → reverse image search for cross-platform confirmation
   - Bio → links, email addresses, secondary usernames, role hints
   - Tagged posts → social circle and associates
   - Captions → location hints, workplace references, behavioral patterns

→ Cross-verify
   - Does the face match WhatsApp / Telegram profile photo?
   - Does the username pattern match handles found on other platforms?
   - Does the bio align with the LinkedIn role?
   - Is the account active or abandoned?
```

**What Instagram exposes on public accounts:** profile photo, bio, post content, tagged posts, follower/following counts, linked external URLs.

**What Instagram does not expose:** private profiles, liked posts, follower identities on private accounts, restricted content. Only publicly visible data is valid OSINT. Do not attempt to bypass privacy settings or interact with the account.

**Analyst reasoning:** Instagram is a confirmation and behavioral layer — not a starting point. Use it to validate identity once you have a name or username from another source, and to understand patterns that professional profiles do not show.

---

## Email Discovery Methods

**Pattern inference** — most orgs use one consistent convention. Find one confirmed employee email and you know the pattern for everyone.

Common patterns:
```
firstname@company.com
firstname.lastname@company.com
f.lastname@company.com
flastname@company.com
```

**Discovery dorks:**
```
"@<DOMAIN>" "<TARGET_NAME>"
"@<DOMAIN>" filetype:pdf | filetype:xls | filetype:csv
site:github.com "@<DOMAIN>"
site:scribd.com "@<DOMAIN>"
```

GitHub embeds author email in every commit by default. Pull commit metadata via the GitHub API and you get emails developers never intended to expose.

**Epieos** — one of the most powerful passive email OSINT tools available. Give it an email address and it returns: Google account confirmation, linked Google services (Maps, Calendar, Photos), profile photo, public Google reviews, and the registered name on the Google account. Google-verified identity pivot from just an email — no login, no notification to the target.

```
https://epieos.com
```

---

## Phone Number Methods

### Dorks First

Before using any tool, run the number through search engines. Business numbers especially surface in directory listings.

```
"<PHONE_NUMBER>"
"<PHONE_NUMBER>" site:justdial.com
"<PHONE_NUMBER>" site:indiamart.com
"<PHONE_NUMBER>" site:sulekha.com
"<PHONE_NUMBER>" filetype:pdf
"<PHONE_NUMBER>" site:facebook.com
site:pastebin.com "<PHONE_NUMBER>"
```

### Tools After

Ranked by signal quality for Indian numbers:

**UPI name lookup** — highest signal, no trace. Initiate a transfer on Google Pay or PhonePe, read the bank-verified legal name, cancel. Done in under 10 seconds.

**Truecaller** — crowdsourced name and carrier. https://www.truecaller.com

**Telegram silent pivot** — add the number, open Telegram. Profile photo, username, bio visible with no notification sent.

**WhatsApp profile grab** — display name, photo, about text visible before any message is sent. Profile photo feeds into reverse image search.

**GetContact** — shows what names others have saved the number as. Reveals organizational role from the crowd.

**Sync.me** — pulls from different sources than Truecaller, sometimes surfaces names it misses.

---

## What Absence of Data Tells You

A clean footprint is itself intelligence.

```
No social media      → deliberate privacy or low online activity
No breach hits       → good hygiene or limited account usage
Inconsistent handles → privacy-aware, Sherlock chain breaks
No GitHub            → non-technical or separate work/personal identity
```

Document what you did not find and why it matters. A target with no breach hits is harder to phish. These are findings, not gaps.

---

## Blue Team Application

| Attack Path | Defensive Action |
|-------------|-----------------|
| Phone → UPI name | Separate personal and work phone numbers |
| Photo → old accounts | Audit and delete forgotten platform accounts |
| Email → breach reuse | Unique passwords per site, MFA on all corporate systems |
| Username → cross-platform | Different handles across platforms |
| LinkedIn → email pattern | Limit public profile, remove contact details |
| Email → Epieos Google pivot | Use a separate email for Google account registration |
