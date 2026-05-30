---
name: prospect-research
description: Phase 1 of the weekly-prospect-outreach routine. Builds a research dossier for a single contact/company from data/active-contacts.md — firmographics, recent news, scoops, intent, contact signals, tech-stack/competitor angle, best-match customer proof, the single strongest personalisation anchor, and a confidence flag. Invoked once per contact by the orchestrator. Can also be run standalone to research one company. Every claim must carry a source URL.
---

# Prospect Research — Freshworks Nordic

## Purpose

Produce one structured **dossier** per contact so the `outreach-drafter`
skill can write sharp, personalised touches. You are given a single row
from `data/active-contacts.md`:

`{ Company, Country, Contact Name, Title, LinkedIn URL }`

You are also told the current run/week (1–4). On **run 1** do a full
research pass. On **runs 2–4** do a lighter refresh: re-confirm the anchor
still holds and surface anything new since last week — do not rebuild from
scratch.

## Tools

| Tool | Purpose |
|---|---|
| ZoomInfo `enrich_companies` | Firmographics: size, industry, HQ, revenue |
| ZoomInfo `enrich_scoops` | Funding, leadership moves, initiatives |
| ZoomInfo `enrich_intent` | Active buying-intent topics |
| ZoomInfo `enrich_news` | Recent company news |
| ZoomInfo `enrich_contacts` | Contact title, tenure, seniority |
| ZoomInfo `account_research` / `contact_research` | Deeper structured pulls if the enrich calls are thin |
| Web search | 30-day company news; public contact activity (talks, quotes, press) |

Use ToolSearch to load the ZoomInfo tools (prefix
`mcp__0de0dfba-...`) when you need them.

## What to gather

### 1. Company snapshot (3–4 facts)
- What they do, in one line.
- Size (employees / revenue band) and HQ.
- Most relevant recent news in the last ~30 days.
- One tech-stack signal if findable (see §3).

### 2. Contact snapshot
- Confirm title and seniority (does it match the priority tiers?).
- Tenure if known (new-in-role is a strong anchor).
- Any recent public activity: conference talks, quotes, articles, posts
  referenced in press. Use the LinkedIn URL from the contact row as the
  identity anchor — **do not scrape LinkedIn**; rely on ZoomInfo + web.

### 3. Tech-stack / competitor angle
Look for current ITSM/CX tooling signals via scoops, job postings, news,
case studies: **ServiceNow, Jira Service Management, HaloITSM, Cherwell,
Zendesk**. A known incumbent is a strong displacement angle.

### 4. Customer-proof match
Read `data/nordic-freshservice-customers.md`. Pick the **1–2 closest
analogues** to this prospect by Industry + Employee band. Note which and
why. This feeds the Week 3 social-proof email.

### 5. Personalisation anchor (pick ONE, label it)
Choose the single strongest hook for outreach and tag it as one of:
- `NEWS` — a recent company development
- `ROLE` — contact signal (new in role, expanded remit, public commentary)
- `TECH` — known incumbent tool / consolidation pain
- `PROOF` — a closely analogous Nordic customer story

State in one line *why* this anchor is the strongest for this person.

### 6. Confidence flag
Assign **HIGH / MEDIUM / LOW** with a one-line reason:
- HIGH — anchor is specific, verified, current, multiple corroborating sources.
- MEDIUM — anchor is plausible but partly inferred or single-source.
- LOW — thin data; anchor is generic. Flag clearly so the drafter keeps it safe.

## Output format (return to orchestrator)

```
CONTACT: [Name], [Title] — [Company] ([Country])
LinkedIn: [URL from active-contacts.md]

COMPANY SNAPSHOT
• [what they do]
• [size / HQ]
• [recent news]                         📎 [source URL]
• [tech-stack signal]                   📎 [source URL]

CONTACT SNAPSHOT
• Title/seniority: [...]                📎 [source URL]
• Tenure: [... or "unknown"]
• Recent activity: [... or "none found"] 📎 [source URL]

TECH / COMPETITOR ANGLE
• [incumbent tool + evidence, or "no clear signal"]  📎 [source URL]

CUSTOMER-PROOF MATCH
• [Customer name] — [why analogous: industry + size]

ANCHOR: [NEWS | ROLE | TECH | PROOF]
Why: [one line]

CONFIDENCE: [HIGH | MEDIUM | LOW] — [one-line reason]
```

## Quality rules

- **Source URL on every factual claim.** No URL → don't state it as fact.
- **"Unknown" is allowed and expected.** Better an honest gap than a guess.
- **Never invent** email addresses, phone numbers, names, or news.
- **Respect compliance.** Legitimate B2B research only; no scraping,
  no bulk extraction. One contact at a time.
- **Stay current.** Prioritise the last 30 days; ignore stale material
  unless it is load-bearing for the anchor.
