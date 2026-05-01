# Analyst Framework

> This is the brain of the playbook. Every execution layer that follows operates inside this framework. Read this first. Return to it when you get lost.

---

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

## 3. Decision Logic

Apply this at every step of an investigation:

```
If signal is strong   → go deeper on this node
If signal is weak     → pivot to a different node
If signal is noisy    → discard, document why, move on
```

Do not chase noise. One strong signal chain beats fifty weak leads.

---

## 4. Signal vs Noise

Not all findings are equal. Classify before you act.

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

Prioritize high signal. Medium signal feeds the graph. Low signal is context — not a finding.

---

## 5. When to Stop

**Stop when:**
- The objective is answered
- Pivots become repetitive (same nodes, different routes)
- Risk is demonstrable with current evidence
- You are past the authorized scope

Continuing past a clear finding wastes time and increases footprint risk. Document what you have. Make the case.

---

## 6. The Six-Phase Methodology

```
Phase 1 — Hypothesis      What am I looking for and why might it exist?
Phase 2 — Passive Recon   Zero footprint. No direct contact with target systems.
Phase 3 — Aggregation     Cross-reference findings across multiple sources.
Phase 4 — Correlation     Chain weak signals until only one identity or path fits.
Phase 5 — Verification    Confirm findings without alerting the target.
Phase 6 — Documentation   Map to frameworks, score risk, write the report.
```

---

## 7. Legal and Ethical Boundaries

- All techniques operate on **publicly available data only**
- Active exploitation is **out of scope** — this is passive recon methodology
- Always obtain **written authorization** before running OSINT against an organization
- Responsible disclosure is mandatory when findings affect real systems
- Governed by applicable law — India: IT Act 2000, DPDP Act 2023

---

## 8. How the Execution Layers Connect

Each section of this playbook is an expansion layer within the Attack Path Model. Use this map to know where you are in an investigation:

| Layer | Section | Role in Framework |
|-------|---------|-------------------|
| Entry Point | Google Dorking | Surface mapping, initial node discovery |
| Infrastructure Expansion | Domain OSINT | Attack surface mapping, subdomain and DNS graph |
| Identity Graph | Individual OSINT | Correlation, identity resolution, credential chains |
| Document Intelligence | Document OSINT | Node enrichment, metadata, pretext material |
| Platform Expansion | Telegram OSINT | Community mapping, threat intel pivot |
| Specialized Expansion | WordPress OSINT | Platform-specific risk reasoning |
| Institutional Expansion | Government OSINT | Domain-specific surface mapping |
| Verification | Tool Reference | Automated confirmation of manual findings |
| Defensive Mirror | Blue Team Detection | Detection logic for each offensive technique |
| Proof of Concept | Case Study | Full chain walkthrough from entry to disclosure |
