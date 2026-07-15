---
name: prospect-research
description: Research phase of the weekly-prospect-outreach routine. Builds a dossier per company in the pipeline (researched once, shared across its 3–4 contacts) using ZoomInfo + Lusha AND public sources — latest news, annual/quarterly reports, exec interviews, sector trends. Produces the firmographic snapshot, incumbent/competitor angle, best-match customer proof, the single strongest personalisation anchor, and a confidence flag. Every claim carries a source URL.
---

# Prospect Research — Freshservice Nordic (multi-source)

## Purpose

Produce one **dossier per company** so `outreach-drafter` can write sharp,
personalised touches. A company is researched **once** and the dossier is shared
across its 3–4 contacts (plus a short per-contact snapshot each).

You are given a company + its contacts from `data/pipeline.md`, the qualifying
signal that put it there, and the current cadence week. On **Week 1** do a full
pass. On **Weeks 2–4** do a lighter refresh: confirm the anchor still holds and
surface anything new since last week.

## Sources — vendors AND public web

| Source | Tools |
|---|---|
| **ZoomInfo** (`mcp__ZoomInfo__*`) | `enrich_companies`, `enrich_scoops`, `enrich_intent`, `enrich_news`, `enrich_company_signals`, `enrich_contacts`, `account_research`, `contact_research` |
| **Lusha** (`mcp__Lusha__*`) | `prospecting_company_enrich`, `prospecting_contact_enrich`, `signals_companies_get`, `website_visits_search` |
| **Public web** | `WebSearch`, `WebFetch` — news, **latest annual & quarterly reports**, **exec interviews**, **sector trends**, job postings |

Do not rely on the two vendors alone — the public-web read is what makes the
anchor specific and credible.

## What to gather

### 1. Company snapshot (3–4 facts)
- What they do, one line.
- Size (employees / revenue band) and HQ.
- Most relevant development in the last ~30 days (news / report / interview).
- One tech-stack / incumbent signal (see §3).

### 2. Public-source deep read (the differentiator)
- **Latest annual / quarterly report:** search for it; pull any stated IT,
  digital-transformation, efficiency, growth or M&A priorities.
- **Recent press & interviews:** especially from the target contacts or their
  execs — priorities, pain, initiatives in their own words.
- **Sector trend context:** what's pressing their industry (cost pressure,
  consolidation, compliance, growth) that ITSM/EX touches.

### 3. Incumbent / competitor angle
Identify the current ITSM/service tool via scoops, job postings, case studies,
news: **HaloITSM** (weight as a live Nordic competitor), **ServiceNow**,
**TOPdesk**, **BMC**, **ManageEngine**, **Ivanti**, **Jira Service Management**,
legacy/free ticketing. Note the maturity band (low vs. cost-constrained-high)
and the matching displacement angle from `data/icp-freshservice-nordic.md`.

### 4. Customer-proof match
Read `data/nordic-freshservice-customers.md`; pick the **1–2 closest analogues**
by industry + employee band. Note which and why (feeds Week-3 Email 3).

### 5. Contact snapshot (per contact, brief)
- Confirm title/seniority and persona (budget owner / champion / I&O).
- Tenure if known (new-in-role is a strong anchor).
- Any recent public activity (talks, quotes, posts) via web — use the LinkedIn
  URL from the pipeline as identity; do **not** scrape LinkedIn.

### 6. Personalisation anchor (pick ONE, label it)
The single strongest hook, tagged:
- `TRIGGER` — a funded project / event (tool retirement, M&A, growth, audit)
- `INCUMBENT` — current tool pain / displacement angle
- `NEWS` — a recent company development / report / interview point
- `ROLE` — contact signal (new in role, expanded remit, public commentary)
- `PROOF` — a closely analogous Nordic customer
State in one line *why* it's strongest.

### 7. Confidence flag
**HIGH / MEDIUM / LOW** + one-line reason. LOW → drafter keeps claims generic.

## Output (per company, return to orchestrator)

```
COMPANY: [name] — [country] — [industry] — [employee band]
Cadence Week: [1–4]   |   Qualifying signal: [from pipeline]

COMPANY SNAPSHOT
• [what they do]
• [size / HQ]
• [recent development]                    📎 [url]
• [incumbent signal]                       📎 [url]

PUBLIC-SOURCE READ
• Report/priorities: [...]                 📎 [url]
• Press/interview: [...]                    📎 [url]
• Sector trend: [...]                       📎 [url]

INCUMBENT / COMPETITOR ANGLE
• [tool + maturity band + displacement angle, or "no clear signal"]  📎 [url]

CUSTOMER-PROOF MATCH
• [Customer] — [why analogous: industry + size]

CONTACTS
• [Name], [Title] — persona: [budget owner/champion/I&O] — tenure: [.. or unknown]
  recent activity: [.. or none found]      📎 [url]
• (repeat for each contact)

ANCHOR: [TRIGGER | INCUMBENT | NEWS | ROLE | PROOF]
Why: [one line]

CONFIDENCE: [HIGH | MEDIUM | LOW] — [reason]
```

## Quality rules
- **Source URL on every factual claim.** No URL → don't state it as fact.
- **"Unknown" is allowed and expected.** Honest gap beats a guess.
- **Never invent** people, emails, phones, news, or metrics.
- **Only cite Freshservice proof figures from the playbook/ICP file** — never
  invented numbers.
- Legitimate B2B research; no scraping, no bulk extraction; one company at a time.
