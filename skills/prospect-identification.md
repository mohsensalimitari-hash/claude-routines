---
name: prospect-identification
description: Multi-source, signal-ranked prospecting engine for Freshservice Nordic. Sources net-new Sweden/Finland companies that fit the Partner Playbook ICP, ranked by publicly-available buying signals (funded triggers, intent, incumbent displacement, news, annual/quarterly reports, exec interviews, sector trends) using ZoomInfo + Lusha + public web. Returns scored companies each with 3–4 ranked contacts. Invoked by the weekly-prospect-outreach routine (which applies the weekly cap) and by the root prospect-identification wrapper for on-demand review.
---

# Prospect Identification Engine — Freshservice Nordic

## Purpose

Surface **net-new** Sweden/Finland companies that fit the ICP, ranked by
**publicly-available buying signals**, each with **3–4 ranked contacts**. The
caller decides how many to take (the weekly routine applies `weekly_add_cap` /
`first_run_seed_cap` from `data/config.md`; the on-demand wrapper just prints
them for review).

## Inputs

| File | Role |
|---|---|
| `data/icp-freshservice-nordic.md` | The ICP (size, industries, triggers, incumbents, personas). **Authoritative** — includes Nordic calibration overriding the raw playbook. |
| `data/nordic-freshservice-customers.md` | Lookalike anchors AND exclusion list (never re-source an existing customer). |
| `data/pipeline.md` | Exclude anything already in the pipeline. |
| `data/dropped-log.md` | Exclude anything still within cooldown. |

## Tools

Load via ToolSearch as needed.

| Source | Tools |
|---|---|
| **ZoomInfo** (`mcp__ZoomInfo__*`) | `search_companies`, `search_intent`, `search_scoops`, `enrich_news`, `enrich_company_signals`, `find_similar_companies`, `get_recommended_contacts`, `search_contacts`, `enrich_companies`, `enrich_contacts` |
| **Lusha** (`mcp__Lusha__*`) | `prospecting_company_search`, `signals_companies_search`, `website_visits_search`, `lookalike_companies`, `decision_makers_search`, `prospecting_contact_search` |
| **Public web** | `WebSearch`, `WebFetch` — news, funding, leadership hires, **annual & quarterly reports**, **exec interviews**, **sector trends** |

## Company sourcing — three lanes

Run all three, then merge and score.

### Lane A — ICP fit (vendors)
ZoomInfo `search_companies` + Lusha `prospecting_company_search`, filtered to:
- HQ / major ops in **Sweden or Finland**
- Size **1,000–5,000** employees (primary band; see exception rule below)
- Industries: **Manufacturing, Financial Services** first, then Business
  Services, Wholesale & Distribution, Healthcare/Pharma, Retail
- Exclude public sector / government / education unless a commercially-run
  state-owned enterprise.

### Lane B — buying signals
- ZoomInfo `search_intent` + `search_scoops` + `enrich_news` + `enrich_company_signals`
- Lusha `signals_companies_search` + `website_visits_search`
- Public web (`WebSearch`/`WebFetch`): recent news, funding rounds, IT/digital
  leadership hires, M&A, **annual/quarterly reports** and **exec interviews**
  that mention ITSM pain, tool consolidation, ServiceNow cost, digital-workplace
  initiatives, service-desk overhaul, or rapid growth.
- Topics that matter: ITSM, "ServiceNow alternative / too expensive", ITSM tool
  migration, employee experience, IT automation, CMDB/ITAM, ESM.

### Lane C — lookalikes
Read `data/nordic-freshservice-customers.md`; for the strongest anchors, run
ZoomInfo `find_similar_companies` + Lusha `lookalike_companies`; keep only
Nordic + in-band results.

## Company scoring (0–100)

Score every merged candidate. **The funded trigger dominates.** Suggested
weights (tune against `signal_score_threshold`):

| Factor | Weight | What earns it |
|---|---|---|
| **Active/funded trigger** | **35** | Tool retirement, merger, rapid growth, audit, service-desk overhaul — with a real timeline. The #1 predictor. |
| Incumbent-displacement signal | 20 | Running HaloITSM / ServiceNow / TOPdesk / BMC / ManageEngine / Ivanti / Jira SM / legacy, with outgrown/cost/complexity friction |
| Recent scoops | 12 | Funding, IT/digital leadership hire, reorg, M&A |
| Intent topics | 12 | Active ITSM/EX/automation intent (ZoomInfo/Lusha) |
| Public-signal strength | 10 | Corroborating news / annual & quarterly report / interview / sector trend |
| ICP size + industry tightness | 8 | Dead-centre of the band + a top-2 industry |
| Web-visit / engagement signal | 3 | Lusha website visits or similar |

**Exception rule for size:** a company **outside 1,000–5,000** may still qualify
**only** if the funded-trigger factor is near-max (very strong, clearly funded
intent). Otherwise drop it. Flag any such exception explicitly in the output.

Only companies **≥ `signal_score_threshold`** qualify. Returning **fewer is
correct** — never pad the list to hit a cap.

## Contact sourcing (3–4 per qualified company)

For each qualified company:
- ZoomInfo `get_recommended_contacts` / `search_contacts` + Lusha
  `decision_makers_search` / `prospecting_contact_search`.
- Filter to the **playbook persona set**:
  - **IT Leader** (CIO / VP / Director of IT) — budget owner
  - **Head of IT Service Delivery / Service Management** — usual champion
  - **Infrastructure & Operations Leader** — uptime / CMDB / assets
  - Supporting signal: specialised **IT Operations / Security** roles indicate
    ITSM readiness.
- **Aim to cover the budget owner + the champion** among the 3–4.
- **Rank contacts** by contact-level signal: recent job change / new-in-role
  (strong), seniority-persona fit, public activity (talks/quotes/posts found via
  web), contact intent. Give each contact a one-line "why this person."

## Exclusions & compliance

- Dedupe against: existing customers, current pipeline, and cooldown log —
  **match on normalized company name + primary domain**, not raw string, so
  legal-suffix variants ("Bonnier News" vs "Bonnier News AB") can't slip through.
- **Respect the caller's headroom.** The weekly routine enforces
  `max_active_companies` and `weekly_add_cap` from `data/config.md`; when the
  pipeline is already at the ceiling it will take **zero** new companies. Return
  the ranked list regardless — the caller decides how many to take. Never pad.
- **Never** re-source a company inside its cooldown window.
- Real companies and real, current people only — **no fabrication**. Verify
  existence with a quick web check when data is thin.
- Every signal (company and contact) carries a **source URL**. "Unknown" is
  allowed; a guess is not.
- Legitimate B2B research only — individual queries, no bulk harvesting
  (ZoomInfo/Lusha terms).

## Output

Return a ranked list. For each company:

```
COMPANY: [name] — [country] — [industry] — [employee band]   SCORE: [0–100]
Why now (company signal): [funded trigger / displacement / etc.]   📎 [source URL]
Incumbent: [tool or "unknown"]                                     📎 [source URL]
[⚠ SIZE EXCEPTION: outside 1,000–5,000 — justified by <trigger>]   (only if applicable)

Contacts (3–4, ranked):
  1. [Name] — [Title] — [LinkedIn URL]
     Why: [contact signal]                                        📎 [source URL]
  2. ...
```

Callers that write to the pipeline map each contact to one `pipeline.md` row
(Cadence Week 1, Status `active`), carrying the company signal, contact signal,
and sources across.
