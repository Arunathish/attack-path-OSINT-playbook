# Attack Path OSINT Playbook

I started writing this after finding exposed infrastructure on an institution's network using nothing but Google and some security testing.

No exploits. No tools. Just search operators and the right questions.

That made me think — if I could map this much from a browser tab, what does a real attacker see before they ever touch a target? This playbook is my attempt to answer that properly.

## What this is

A field guide built around one idea: **OSINT is not a tool problem, it's a thinking problem.**

Most guides hand you a list of commands and call it a methodology. This one works the other way — every section starts with the analyst question, builds the attack path, then references tools at the end as execution aids.

Every offensive technique is paired with its defensive equivalent. Because if you understand how something is found, you understand how to stop it from being found.

## Why I built it this way

I've gone through enough OSINT resources that start with "install theHarvester" and end with a wall of dork queries. They're fine as references. They don't teach you to think.

The questions that actually matter during an investigation are:

- What am I looking for, and why would it exist publicly?
- Is this signal or noise?
- When do I go deeper? When do I pivot? When do I stop?

Those questions don't appear in most guides. They're in every section of this one.

Start with 01 — Analyst Framework. Every module that follows operates inside that framework. Techniques without a framework produce noise.

## Structure

```
modules/
├── 01-analyst-framework.md        ← Start here. Always.
├── 02-google-dorking.md
├── 03-domain-osint.md
├── 04-individual-osint.md
├── 05-document-intelligence.md
├── 06-metadata-intelligence.md
├── 07-reverse-image-geolocation.md
├── 08-social-media-osint.md
├── 09-telegram-osint.md
├── 10-wordpress-osint.md
├── 11-government-osint.md
├── 12-blue-team-detections.md
├── 13-tool-reference.md
└── 14-case-study.md
```

---

## Table of Contents

| # | Module | Role in Framework |
|---|--------|-------------------|
| 01 | [Analyst Framework](./modules/01-analyst-framework.md) | **Core — read first** |
| 02 | [Google Dorking](./modules/02-google-dorking.md) | Entry Point / Data Exposure |
| 03 | [Domain OSINT](./modules/03-domain-osint.md) | Infrastructure Expansion |
| 04 | [Individual OSINT](./modules/04-individual-osint.md) | Identity Graph Building |
| 05 | [Document Intelligence](./modules/05-document-intelligence.md) | Node Enrichment |
| 06 | [Metadata Intelligence](./modules/06-metadata-intelligence.md) | Deep Enrichment / Pivot Generation |
| 07 | [Reverse Image & Geolocation](./modules/07-reverse-image-geolocation.md) | Visual Intelligence Layer |
| 08 | [Social Media OSINT](./modules/08-social-media-osint.md) | Behavioral & Identity Layer |
| 09 | [Telegram OSINT](./modules/09-telegram-osint.md) | Platform Expansion |
| 10 | [WordPress OSINT](./modules/10-wordpress-osint.md) | Specialized Platform Recon |
| 11 | [Government OSINT](./modules/11-government-osint.md) | Institutional Surface Mapping |
| 12 | [Blue Team Detections](./modules/12-blue-team-detections.md) | Defensive Mirror |
| 13 | [Tool Reference](./modules/13-tool-reference.md) | Automated Verification |
| 14 | [Case Study](./modules/14-case-study.md) | Full Chain Walkthrough |

---

---

## The pivot chain that started this

```
Phone number
  → UPI lookup → bank-verified legal name
  → LinkedIn → employer → email pattern
  → Breach data → password reuse → username
  → Sherlock → old accounts → profile photo
  → Yandex → older accounts → deeper data
```

Each step is passive. Each step uses only public data. That chain from a phone number to a full identity picture is what made me realize most people have no idea what their own exposure surface looks like.

---

---

## A few things worth mentioning

**It's India-grounded.** UPI name lookups, Truecaller chains, JustDial and IndiaMart dorks, DPDP Act 2023 framing. Not a recycled Western playbook.

**The case study is real.** Found exposed infrastructure on my college network through passive recon. Documented it, reported it responsibly, wrote it up as a full walkthrough. It's in module 14.

**Blue team is not an afterthought.** Every module has a detection and hardening table. I work in MDR context so the defensive side matters as much as the offensive side to me.

## Contributing

If you find something wrong, outdated, or missing — open an issue or PR. This is a living document.


