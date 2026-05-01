# OSINT Playbook

> A practitioner's field guide to Open Source Intelligence — method-driven, analyst-focused, tools come last.

**Author:** Rishi  
**Focus:** Attack Surface Management, Corporate Recon, Identity Enumeration  
**Framing:** Every offensive technique paired with its defensive detection equivalent

---

## Philosophy

Most OSINT guides hand you a tool list and call it a framework.

This is different.

Before touching a single tool or search query, an analyst needs to know:

- What am I looking for, and why would it exist publicly?
- How do I classify signal vs. noise?
- When do I go deeper? When do I pivot? When do I stop?

**Tools come last. Thinking comes first.**

Every section in this playbook is an execution layer within a structured analyst framework — built around attack paths, pivot chains, and decision logic. Not checklists.

---

## Start Here

**→ [01 — Analyst Framework](./modules/01-analyst-framework.md)**

This is the brain of the playbook. Read it before anything else. Every module that follows operates inside this framework.

> Techniques without a framework produce noise.

---

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

## What Makes This Different

**Method-driven, not tool-driven.**  
Every section leads with the analyst workflow and attack path model. Tools are referenced at the end of each section as execution aids — not the starting point.

**Blue team paired with every offensive technique.**  
Each module includes a detection and hardening table. Understanding how to find things is only half the picture.

**India-specific grounding.**  
UPI name lookup chains, Truecaller, JustDial/Indiamart dorks, and DPDP Act 2023 framing — not just a recycled Western playbook.

**Pivot-chain focused.**  
Every section is built around the question: *what does this data connect to next?*

```
Phone → Legal Name (UPI) → LinkedIn → Email Pattern
     → Breach Data → Username → Cross-Platform Accounts
     → Profile Photo → Reverse Image → Older Accounts
```

---

## Legal and Ethical Boundaries

- All techniques operate on **publicly available data only**
- Active exploitation is **out of scope** — this is passive recon methodology
- Always obtain **written authorization** before running OSINT against an organization
- Responsible disclosure is mandatory when findings affect real systems
- Governed by applicable law — **India: IT Act 2000, DPDP Act 2023**

---

## Contributing

Found a gap, a broken tool link, or want to add a module?  
Open an issue or submit a pull request. All contributions welcome.

---

## License

MIT License — use freely, credit appreciated.
