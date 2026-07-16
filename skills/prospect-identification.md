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
| **ZoomInfo** (`mcp__ZoomInfo__*`) | `search_companies`, `lookup` (tech-vendors → tech-products for incumbent tags), `search_intent`, `search_scoops`, `enrich_news`, `enrich_company_signals`, `find_similar_companies`, `get_recommended_contacts`, `search_contacts`, `enrich_companies`, `enrich_contacts` |
| **Lusha** (`mcp__Lusha__*`) | `prospecting_company_search`, `signals_companies_search`, `website_visits_search`, `lookalike_companies`, `decision_makers_search`, `prospecting_contact_search` |
| **Public web** | `WebSearch`, `WebFetch` — news, funding, leadership hires, **annual & quarterly reports**, **exec interviews**, **sector trends** |
| **LinkedIn Sales Navigator** *(manual, AE-driven)* | Not an API here. Used as the **contact-gap fallback** (see Contact sourcing) — the routine emits a ready Sales Nav search for the AE to check by hand when ZoomInfo + Lusha come up thin. |

## Company sourcing — four lanes

Run all four, then merge and score. Lanes A/B/D are the workhorses; Lane C
(lookalikes) is a supplement — in practice it returns a lot of out-of-band /
non-Nordic noise, so treat its output as candidates to verify, not to trust.

### Lane A — ICP fit (vendors)
ZoomInfo `search_companies` + Lusha `prospecting_company_search`, filtered to:
- **HQ in Sweden or Finland — hard filter.** Use ZoomInfo
  `locationSearchType: HQ` (not `Person`/`PersonOrHQ`), so large multinationals
  with only a local office don't leak in and the buying committee is in-territory.
  A Finland-HQ group (e.g. a former Cargotec/Hiab) is in-territory but flag it as
  a Finland account, not Sweden.
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
Nordic + in-band results. **Supplement only** — this lane is noisy; verify every
hit against the HQ + size + industry filters before it earns a slot.

### Lane D — incumbent displacement (tech-stack)
Directly surface companies **running a competitor ITSM tool** — the strongest,
most reliable displacement signal, and the one that fires the 20-pt factor below
without guessing. Two steps:
1. Resolve incumbent tool product tags once via ZoomInfo `lookup`
   (`tech-vendors` → `tech-products`, passing the `vendor`). Known-good tags:
   **ServiceNow IT Service Management = `27910`**, generic **ServiceNow = `18023`**
   (also useful: CMDB `122645`, Incident Mgmt `122653`). Resolve TOPdesk, Jira
   Service Management (Atlassian), ManageEngine, BMC, Ivanti, HaloITSM the same way.
2. `search_companies` with `techAttributeTagList: <tags>`, `country: sweden`
   (and/or finland), `locationSearchType: HQ`, `employeeCount: 1000to4999`.
Every company this returns has a **confirmed incumbent** — carry the tool name +
"ZoomInfo tech tag" as the displacement source. HaloITSM / TOPdesk / legacy skew
low-maturity (lead with "what proper ITSM unlocks"); ServiceNow / BMC skew
cost/complexity ("more for less"). Cross-check high-scorers from Lanes A/B against
this lane to replace an "unknown" incumbent with a confirmed one.

## Company scoring (0–100)

Score every merged candidate. **The funded trigger dominates.** Suggested
weights (tune against `signal_score_threshold`):

| Factor | Weight | What earns it |
|---|---|---|
| **Active/funded trigger** | **35** | Tool retirement, merger, rapid growth, audit, service-desk overhaul — with a real timeline. The #1 predictor. |
| Incumbent-displacement signal | 20 | Running HaloITSM / ServiceNow / TOPdesk / BMC / ManageEngine / Ivanti / Jira SM / legacy, with outgrown/cost/complexity friction. **Detect directly via ZoomInfo tech-product tags (Lane D)**; award full weight on a confirmed tag, partial on a web-inferred tool, and leave "unknown" only when both tag and web come up empty. |
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
- **Prefer in-territory contacts.** Filter to Sweden/Finland-based people first
  (ZoomInfo `locationSearchType: Person` + `country`; Lusha returns `location`).
  Only fall back to group-level/overseas IT leaders when the buying committee
  genuinely sits at HQ abroad — and flag it when you do (a "IT Service Delivery"
  director sitting in the UK is a weaker Sweden-run contact than a local Head of IT).
- **Rank contacts** by contact-level signal: recent job change / new-in-role
  (strong), seniority-persona fit, public activity (talks/quotes/posts found via
  web), contact intent. Give each contact a one-line "why this person."

### Contact-gap fallback → LinkedIn Sales Navigator (manual)
ZoomInfo + Lusha run thin on **federated holding groups and serial-acquirer
distributors** (decentralised IT lives in the subsidiaries, not a central team) —
seen repeatedly with names like Addtech, Alligo, AddLife, Duni, Alimak, Inwido.
When a company **clears the score threshold but yields fewer than 3 usable
IT-leadership contacts**:
1. Set a **`contactability` flag** on the company: `thin — central IT federated`.
2. Do **not** silently drop it or pad with off-persona names. If it's worth
   pursuing, it's worth a manual check.
3. Emit a ready **LinkedIn Sales Navigator** search for the AE to run by hand,
   pre-filled with: current company = `<name>`, geography = Sweden (or Finland),
   and seniority/title keywords `CIO OR "Head of IT" OR "IT Director" OR "Service
   Management" OR "Service Delivery" OR "Infrastructure" OR "IT Operations"`.
   Provide it as a Sales Nav people-search URL, e.g.
   `https://www.linkedin.com/sales/search/people?query=<company+titles+geo>`,
   so the AE can open, eyeball, and pull contacts LinkedIn has but the data
   vendors miss. This is a **manual, human-in-the-loop** step — the routine never
   scrapes LinkedIn; it only hands the AE the pre-built query.

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
COMPANY: [name] — [country/HQ] — [industry] — [employee band]     SCORE: [0–100]
Why now (company signal): [funded trigger / displacement / etc.]   📎 [source URL]
Incumbent: [tool + "ZoomInfo tech tag" / web-inferred / "unknown"] 📎 [source URL]
[⚠ SIZE EXCEPTION: outside 1,000–5,000 — justified by <trigger>]   (only if applicable)
[⚠ CONTACTABILITY: thin — central IT federated → Sales Nav check]  (only if applicable)

Contacts (3–4, ranked):
  1. [Name] — [Title] — [location] — [LinkedIn URL]
     Why: [contact signal]                                        📎 [source URL]
  2. ...
[Sales Nav fallback (only if contactability is thin):
   https://www.linkedin.com/sales/search/people?query=... ]
```

Callers that write to the pipeline map each contact to one `pipeline.md` row
(Cadence Week 1, Status `active`), carrying the company signal, contact signal,
and sources across.
