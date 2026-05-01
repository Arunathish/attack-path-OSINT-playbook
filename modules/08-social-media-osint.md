# Facebook & Instagram OSINT

**Role in Framework:** Identity Graph Expansion and Behavioral Intelligence layer. Facebook gives you the deepest personal data — real names, family connections, social circle, and location history. Instagram gives you visual identity confirmation, behavioral patterns, and corporate exposure through imagery. Together they cover what professional profiles deliberately hide.

---

# Part 1 — Facebook OSINT

## What Facebook Exposes

Facebook was built around real identity. Even with privacy settings applied, public profiles leak more than most people realize.

**From a public profile:**
- Full legal name (often confirmed by friend tagging)
- Profile photo and photo history (reverse image searchable)
- Cover photo (often reveals location, workplace, events)
- Bio — workplace, education, hometown, current city
- Relationship status and family connections
- Linked accounts (Instagram cross-post, check-ins)
- Public posts — locations, events, associates, opinions
- Groups — community memberships, interests, professional affiliations
- Pages liked — lifestyle, political leaning, religious affiliation
- Check-ins — frequently visited locations, travel patterns
- Tagged photos — social circle mapping, physical appearance across time

**From a semi-public profile (common misconfiguration):**
- Friends list visible while posts are restricted
- About section partially visible (employer, education, city)
- Older posts public before privacy settings were tightened

---

## Layer 1 — Discovery

### Google Dorking

Facebook profiles are partially indexed by Google. Dorking finds profiles that native Facebook search misses.

```
site:facebook.com "<FULL_NAME>"
site:facebook.com "<FULL_NAME>" "<CITY>"
site:facebook.com "<FULL_NAME>" "<EMPLOYER>"
site:facebook.com "<USERNAME>"
site:facebook.com/groups "<TOPIC>" "<COMPANY>"
site:facebook.com "<EMAIL>"
site:facebook.com "<PHONE_NUMBER>"
```

**What dorking surfaces:**
- Profile pages with name and location visible in meta description
- Group memberships indexed by Google
- Public posts mentioning the target's name
- Pages associated with a company or person

### Native Facebook Search

Use Facebook's search bar for queries that Google does not index:

```
People named <TARGET_NAME> who live in <CITY>
People named <TARGET_NAME> who work at <COMPANY>
Posts by <TARGET_NAME>
Photos of <TARGET_NAME>
Groups joined by <TARGET_NAME> (if friends list is visible)
```

### Username Direct Access

If a username is known from another OSINT layer:
```
https://facebook.com/<USERNAME>
https://facebook.com/profile.php?id=<NUMERIC_ID>
```

Numeric profile IDs persist even when usernames change. If you find an old post with a profile ID, it still resolves.

---

## Layer 2 — Profile Analysis

### Identity Confirmation

```
Profile photo
→ Reverse image search (Yandex, Google Images, TinEye)
→ Does this face appear elsewhere? On LinkedIn? On Instagram?
→ Older profile photos visible? → Shows appearance over time

Name on profile
→ Cross-reference with UPI lookup result
→ Cross-reference with LinkedIn full name
→ Consistency across three sources = high confidence
```

### About Section Intelligence

```
Workplace listed
→ Confirm on LinkedIn
→ Infer email pattern
→ Pivot to Domain OSINT

Education listed
→ Graduation year reveals approximate age
→ University connections reveal social circle

Hometown vs. Current City
→ Gap between the two = relocated for work
→ Current city narrows physical location
```

### Social Circle Mapping

```
Friends list (if visible)
→ Identify colleagues (mutual employer in bios)
→ Identify family (shared surname, family relationship posts)
→ Identify close associates (frequent mutual interactions in public posts)

Tagged photos
→ Who appears repeatedly? → Core social circle
→ Where are they taken? → Location patterns

Group memberships
→ Professional groups → industry, employer, role
→ Local groups → confirms geographic location
→ Interest groups → behavioral profile
```

---

## Layer 3 — Corporate Recon via Facebook

### Employee Discovery

```
site:facebook.com "<COMPANY_NAME>" "works at"
site:facebook.com "<COMPANY_NAME>" employees
Facebook search: People who work at <COMPANY>
```

This surfaces names that do not appear on LinkedIn — junior staff, operations, sales, and support roles are underrepresented on LinkedIn but common on Facebook.

### Company Page Intelligence

```
https://facebook.com/<COMPANY_PAGE>
```

**What company pages expose:**
- Employee posts and tagged mentions
- Check-ins by employees at office locations (confirms physical addresses)
- Events → reveals internal meetups, hiring events, product launches
- Reviews → disgruntled employees or customers sometimes reveal internal details
- Who manages the page → admin names sometimes visible

### Group Intelligence

Internal company groups are sometimes public or discoverable:

```
site:facebook.com/groups "<COMPANY_NAME>"
Facebook search: Groups — "<COMPANY_NAME>"
```

Internal discussion groups left public are a significant intelligence source — hiring discussions, internal announcements, and operational details surface in them.

---

## Attack Path: Facebook Profile → Full Identity Graph

```
Starting Node: Name + City (from phone lookup or LinkedIn)

Step 1 — Discovery
→ site:facebook.com "<NAME>" "<CITY>"
→ Facebook native: People named <NAME> in <CITY>
→ Identify candidate profiles

Step 2 — Profile Confirmation
→ Profile photo → reverse image search → matches LinkedIn / WhatsApp?
→ Employer in About section → matches LinkedIn?
→ Education details → consistent with known data?

Step 3 — Social Circle Extraction
→ Friends list visible? → map colleagues and family
→ Tagged photos → identify recurring individuals
→ Group memberships → professional + geographic confirmation

Step 4 — Behavioral Intelligence
→ Public post history → lifestyle, location patterns, interests
→ Check-ins → frequently visited locations
→ Event attendance → confirms location + community

Step 5 — Pivot Targets Identified
→ Family members → potential pretexting angles
→ Colleagues → expand employee graph
→ Location data → physical security context

Step 6 — Corporate Pivot
→ Employer confirmed → Domain OSINT
→ Email pattern inferred from name format
→ Colleagues on Facebook → build employee list without LinkedIn
```

---

## Attack Path: Company Page → Employee Enumeration

```
Starting Node: Company name

Step 1 — Find the company page
→ https://facebook.com/<COMPANYNAME>
→ site:facebook.com "<COMPANY_NAME>"

Step 2 — Extract from page
→ Employees tagged in posts → name list begins
→ Check-ins at office locations → physical address confirmed
→ Events → internal calendar visible

Step 3 — Employee profile pivot
→ For each name found: repeat individual profile analysis
→ Build org graph from Facebook connections alone

Step 4 — Cross-reference with LinkedIn
→ Match Facebook names to LinkedIn profiles
→ Facebook often reveals roles LinkedIn does not (ops, support, admin)
```

---

## Facebook — Analyst Decision Points

```
Does the name match across Facebook, LinkedIn, and UPI?
Is the employer consistent across platforms?
Are the tagged photos consistent with the profile photo?
How recent is the activity? Inactive accounts = stale data
Is the friends list visible? → multiplies available pivots
Are there inconsistencies in location data? → may indicate a duplicate profile or alias
```

```
High confidence:   Name + photo + employer confirmed across 3+ sources
Medium confidence: Name and photo match, employer inferred
Low confidence:    Name only, no photo match, no corroboration
```

---

## What Facebook Does Not Expose

Only publicly visible profile data is valid OSINT. Do not attempt to bypass privacy settings, create fake accounts to access restricted content, or interact with the target.

```
Private profiles where posts, photos, and friends list are restricted
Messages and private group content
Data behind authentication walls
```

---

## Facebook — Blue Team

| Technique | Server-Side Logs? | Notes |
|-----------|------------------|-------|
| Google dorking `site:facebook.com` | No | Passive, external |
| Direct profile access | Facebook-side only | No notification to profile owner |
| Facebook native search | Facebook-side only | No notification |
| Group discovery | Facebook-side only | Public groups only |

| Exposure | Defensive Action |
|----------|-----------------|
| Employer listed publicly | Remove from About section, or restrict to Friends only |
| Friends list visible | Set to Friends only or Only Me |
| Tagged photos public | Review and restrict photo tags |
| Check-ins visible | Disable location sharing in posts |
| Public post history | Audit via Facebook's Privacy Checkup |
| Group memberships visible | Set group membership visibility to Only Me |
| Profile photo reverse searchable | Use a photo not shared on any other platform |

```
Employee checklist:
[ ] Audit Facebook privacy settings annually
[ ] Remove employer from public About section
[ ] Set friends list to Friends only
[ ] Review tagged photo visibility
[ ] Avoid check-ins at office locations
[ ] Do not join company-named public groups using personal accounts
```

---
---

# Part 2 — Instagram OSINT

## What Instagram Exposes

Instagram is image-first, which makes it uniquely valuable. Every post is a data point — not just text, but metadata, faces, locations, and behavioral patterns.

**From a public profile:**
- Profile photo — face, consistent with other platforms
- Bio — name, links, email address, username variants, employer hints
- Linked external URL — often a personal site, portfolio, or Linktree
- Posts — visual content, location tags, caption text, hashtags
- Tagged posts — social circle, associates, events
- Reels and Stories highlights — behavioral patterns, interests, locations
- Follower/following counts — reach and community size
- Who they follow — professional interests, personal affiliations
- Comments on public posts — associates, tone, language

**What Instagram does not expose:**
- Private profiles — all content is locked
- Liked posts
- Direct messages
- Follower identities on private accounts

Only publicly visible data is valid OSINT. Do not interact with the account or attempt to bypass privacy settings.

---

## Layer 1 — Discovery

### Google Dorking

```
site:instagram.com "<USERNAME>"
site:instagram.com "<FULL_NAME>"
site:instagram.com "<NAME>" "<COMPANY>"
site:instagram.com "<NAME>" "<CITY>"
"instagram.com/<USERNAME>"
```

**What dorking surfaces:**
- Profile pages with bio text visible in Google's snippet
- External sites embedding Instagram posts (reveals username even if account is now private)
- News articles or blog posts referencing an Instagram handle

### Username Pivot

If a username was found during phone OSINT, email OSINT, or Sherlock enumeration, test it directly:

```
https://instagram.com/<USERNAME>
```

Usernames are frequently reused across platforms. Always test handles found elsewhere.

### Reverse Username Pattern Testing

If you have a naming pattern (e.g., `john.doe` from LinkedIn):

```
site:instagram.com "john.doe"
site:instagram.com "johndoe"
site:instagram.com "j.doe"
```

Instagram profiles are often found via Google before Instagram's own search surfaces them, especially for low-follower accounts.

---

## Layer 2 — Profile Analysis

### Identity Confirmation

```
Profile photo
→ Reverse image search immediately (Yandex, Google Images, TinEye)
→ Does this face appear on LinkedIn, Facebook, or WhatsApp?
→ Photo grid → physical appearance over time

Username pattern
→ Matches handles found on other platforms? → same person
→ Slight variation (john.doe vs johndoe_) → privacy-aware, same person
→ Completely different → possible separate personal account

Bio
→ Name in bio → cross-reference with UPI / LinkedIn
→ Employer hint → "@ CompanyName" or role description
→ Alternate usernames → pivot immediately
→ Linked URL → highest-value element on the profile (see below)
```

### Bio Link Analysis

The linked URL in an Instagram bio often contains more intelligence than the profile itself.

```
Personal website  → name, portfolio, email contact form
Linktree          → expands to all linked platforms at once
LinkedIn          → professional identity confirmed
GitHub            → technical identity confirmed
YouTube           → content, voice, face, behavioral patterns
Email contact     → direct address
```

Always fetch the linked URL. A Linktree link expands the entire platform graph in one step.

### Post Content Intelligence

```
Location tags
→ Confirms physical presence in city, neighborhood, venue
→ Recurring locations → home area, workplace area, regular spots

Caption text
→ Workplace references → employer confirmation
→ Event references → industry events attended
→ Language and terminology → professional domain

Hashtags
→ Industry hashtags → professional role and interests
→ Local hashtags → geographic confirmation
→ Community hashtags → affiliations

Post timing patterns
→ Consistent posting time → timezone and schedule
→ Posting from events → physical location at specific dates
```

### Social Circle Mapping

```
Tagged posts (if visible)
→ Who tags the target? → close associates
→ Who is the target tagged with repeatedly? → core circle
→ Tag + location = physical presence of multiple people confirmed

Comments on public posts
→ Who comments consistently? → friends, colleagues, family
→ Employer name-drops in comments → colleague confirmation
→ @mentions → direct links to other profiles

Following list (if visible)
→ Does the target follow their own employer account?
→ Colleagues followed → builds employee graph
→ Industry figures followed → professional domain confirmed
```

---

## Layer 3 — Corporate Recon via Instagram

### Employee Discovery via Company Account

```
https://instagram.com/<COMPANY_HANDLE>

→ Tagged employees in posts → name + face
→ Employee takeover content (employer branding) → role + name + face
→ Event and team photos → group images of staff = multiple targets
→ Caption @mentions → direct links to employee personal accounts
```

### Branded Hashtag Enumeration

Companies often use internal culture hashtags:

```
site:instagram.com "#<COMPANYNAME>life"
site:instagram.com "#teamat<COMPANYNAME>"
site:instagram.com "#<COMPANYNAME>team"
```

Employees posting with company hashtags self-identify their employer — and link their personal Instagram directly to the corporate target.

### Office Location Confirmation via Location Tags

```
Search the company office address as an Instagram location tag
→ Posts tagged at that location from employee accounts
→ Identity confirmation + employee list building
```

---

## Attack Path: Instagram Profile → Full Behavioral Picture

```
Starting Node: Username (from Sherlock, phone OSINT, or email OSINT)

Step 1 — Profile Access
→ https://instagram.com/<USERNAME>
→ Public? Proceed. Private? Note username, pivot elsewhere.

Step 2 — Identity Confirmation
→ Profile photo → reverse image search
→ Bio name → cross-reference with LinkedIn / UPI
→ Bio employer → confirm on LinkedIn

Step 3 — Bio Link Expansion
→ Fetch the linked URL
→ Linktree? → extract all linked platforms
→ Personal site? → check for email, portfolio, other handles

Step 4 — Post Analysis
→ Location tags → map geographic patterns (home area, workplace area)
→ Caption text → workplace references, events, terminology
→ Post timing → timezone and schedule patterns
→ Hashtags → professional domain and community affiliations

Step 5 — Social Circle Extraction
→ Tagged posts → identify recurring individuals
→ Comment analysis → extract associate handles
→ Following list → colleagues, employer account

Step 6 — Pivot to Associates
→ For each recurring associate → repeat profile analysis
→ Shared employer? → corporate recon expands
→ Shared location? → physical location confirmed

Outcome:
→ Visual identity confirmed + behavioral patterns mapped + social graph partial + location patterns established
```

---

## Attack Path: Company Account → Employee List

```
Starting Node: Company Instagram handle

Step 1 — Company account analysis
→ Tagged employees in posts
→ @mentions in captions
→ Event and team photos

Step 2 — Branded hashtag enumeration
→ Employees posting with company hashtags → self-identified + personal account linked

Step 3 — Location tag pivot
→ Search office address as Instagram location
→ Employee posts tagged there → identity + personal account

Step 4 — Cross-reference
→ Match Instagram names to LinkedIn profiles
→ Face from Instagram confirms LinkedIn profile photo match
→ Instagram bio email (common for creators/freelancers) → direct contact found

Outcome:
→ Employee list with personal Instagram accounts, faces, and behavioral data
```

---

## Instagram — Analyst Decision Points

```
Is the profile public or private?
Private = note the username, pivot to other platforms

Does the profile photo match across Instagram, LinkedIn, and WhatsApp?
Match across 3 sources = high confidence identity confirmed

How recent is the last post?
Last post > 1 year ago = stale data, lower confidence
Active account = behavioral data is current

Is the bio link a Linktree?
Always expand it — it may be the single most valuable element on the profile

Are location tags present?
Map them — recurring locations reveal home area and workplace area

Are tagged photos visible?
Social circle mapping becomes possible immediately
```

```
High confidence:   Face match + name match + employer match across 3+ sources
Medium confidence: Username match + face match, employer inferred from bio
Low confidence:    Username match only, no photo, no corroborating identity data
```

---

## Instagram — Blue Team

| Technique | Server-Side Logs? | Notes |
|-----------|------------------|-------|
| Google dorking `site:instagram.com` | No | Passive, external |
| Direct profile access | Instagram-side only | No notification |
| Hashtag search | Instagram-side only | No notification |
| Location tag search | Instagram-side only | No notification |

| Exposure | Defensive Action |
|----------|-----------------|
| Public personal profile with employer in bio | Switch to private, or remove employer reference |
| Email address in bio | Remove — use DMs or external contact form |
| Location tags on posts | Disable, or only tag after leaving the location |
| Tagged photos visible | Restrict tag visibility in settings |
| Company hashtag usage | Train employees — hashtag posts link personal accounts to employer |
| Bio Linktree linking all platforms | Audit what the Linktree exposes |
| Office location posts | Do not post check-ins or tag office location |

```
Employee checklist:
[ ] Set personal Instagram to private if it contains employer information
[ ] Remove employer and email from bio
[ ] Disable or review location tags before posting
[ ] Audit tagged photos — restrict unwanted tags in settings
[ ] Do not use company hashtags from personal accounts
[ ] Review what your Linktree links to
```

> **Self-audit for organizations:** Search your company name and branded hashtags using the methods above. Every employee personal account that surfaces with employer data, location tags, or team photos is an exposure finding.

---
---

# Cross-Platform: Where Facebook and Instagram Converge

The two platforms are owned by the same company and frequently cross-linked. Use this when one platform is restricted.

```
Instagram account private?
→ Check if the same account cross-posts to Facebook (linked accounts)
→ Facebook profile may be less restricted
→ Same profile photo → confirm it is the same person

Facebook profile restricted?
→ Instagram may be public with behavioral data Facebook hides
→ Bio link on Instagram may lead to a personal site with contact details

Both restricted?
→ Profile photo from either → reverse image search
→ Username pattern → Sherlock → other platforms
→ Pivot to email OSINT or phone OSINT layer
```

When both platforms are active and linked, information from one fills gaps in the other. Build the graph across both before concluding a target has a clean footprint.