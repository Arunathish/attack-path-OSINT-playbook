# Analyst Framework

## 1. Analyst Mindset

Before touching a tool or a search box, ask four questions:

```
What am I trying to find?
Why would it exist publicly?
What does finding it mean?
What does not finding it mean?
```

Most people open a cheat sheet and start firing queries. That produces noise. An analyst starts with a hypothesis and uses techniques to confirm or eliminate it.

Absence of data is also intelligence. Document what you did not find — and why that matters.

---

## 2. Attack Path Model

Every data point is a node.

Your job is not to collect data. Your job is to expand and connect nodes.

```
Starting Node
     ↓
Expand         → What other nodes does this connect to?
     ↓
Correlate      → What does this node confirm or contradict?
     ↓
Pivot          → Which connected node has the highest signal?
     ↓
Verify         → Is this finding real, current, and meaningful?
```

Each layer of the playbook is an expansion technique. You are always building a graph — not a list.

---

## 3. Signal vs Noise

**High Signal**
- Credentials (plaintext passwords, API keys, tokens)
- Internal documents (org charts, reports, configs)
- Admin access or unauthenticated panels
- Breach data with password reuse potential

**Medium Signal**
- Email addresses and username patterns
- Subdomains (especially dev, staging, internal tooling)
- PDF metadata revealing internal structure
- Tech stack fingerprints

**Low Signal**
- Static marketing pages
- Public-facing documentation (unless it reveals structure)
- Generic social profiles with no sensitive content

---

## 4. The Six-Phase Methodology

```
Phase 1 — Hypothesis      What am I looking for and why might it exist?
Phase 2 — Passive Recon   Zero footprint. No direct contact with target systems.
Phase 3 — Aggregation     Cross-reference findings across multiple sources.
Phase 4 — Correlation     Chain weak signals until only one identity or path fits.
Phase 5 — Verification    Confirm findings without alerting the target.
Phase 6 — Documentation   Map to frameworks, score risk, write the report.
```

---

## 5. Legal and Ethical Boundaries

- All techniques operate on **publicly available data only**
- Active exploitation is **out of scope** — this is passive recon methodology
- Always obtain **written authorization** before running OSINT against an organization
- Responsible disclosure is mandatory when findings affect real systems
- Governed by applicable law — India: IT Act 2000, DPDP Act 2023

---

## 6. How the Execution Layers Connect

Each section of this playbook is an expansion layer within the Attack Path Model. Use this map to know where you are in an investigation:

| Layer | Section | Role in Framework |
|-------|---------|-------------------|
| Entry Point | [Google Dorking](./modules/02-google-dorking.md)| Surface mapping, initial node discovery |
| Infrastructure Expansion | [Domain OSINT](./modules/03-domain-osint.md) | Attack surface mapping, subdomain and DNS graph |
| Identity Graph | [Individual OSINT](./modules/04-individual-osint.md) | Correlation, identity resolution, credential chains |
| Document Intelligence | [Document Intelligence](./modules/05-document-intelligence.md) | Node enrichment, metadata, pretext material |
| Platform Expansion | [Telegram OSINT](./modules/09-telegram-osint.md) | Community mapping, threat intel pivot |
| Specialized Expansion | [WordPress OSINT](./modules/10-wordpress-osint.md) | Platform-specific risk reasoning |
| Institutional Expansion | [Government OSINT](./modules/11-government-osint.md) | Domain-specific surface mapping |
| Verification | [Tool Reference](./modules/13-tool-reference.md) | Automated confirmation of manual findings |
| Defensive Mirror | [Blue Team Detections](./modules/12-blue-team-detections.md)| Detection logic for each offensive technique |
| Proof of Concept | [Case Study](./modules/14-case-study.md) | Full chain walkthrough from entry to disclosure |
