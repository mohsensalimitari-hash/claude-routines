---
name: prospect-identification
description: Standalone, on-demand prospecting skill for a Freshworks Nordic Lead AE. Surfaces ~10 net-new candidate companies and one contact each (Sweden/Finland, ITSM/EX fit) across three lanes — ICP fit, buying signals, and lookalikes of current customers — for the user to review before hand-populating data/active-contacts.md. Triggers on "find new prospects", "surface candidates", "prospect identification", "new accounts to target". NOT scheduled and NOT part of the weekly outreach routine — run it manually when you need fresh candidates.
---

# Prospect Identification — Freshworks Nordic (on-demand)

## Purpose

Surface a **reviewed candidate list (~10 companies, one contact each)** for
the user to look over and selectively hand-copy into
`data/active-contacts.md`. Selling **Freshservice** (ITSM / EX) into
**Sweden and Finland**.

This skill is **standalone and on-demand**. It is NOT scheduled and is NOT
invoked by `weekly-prospect-outreach`. It **does not write** to
`data/active-contacts.md` — the user owns that file and copies picks across
manually.

## Inputs it reads

| File | Role |
|---|---|
| `data/icp-freshservice-nordic.md` | The ICP profile that defines fit |
| `data/nordic-freshservice-customers.md` | Anchors for lookalike sourcing |

## Tools

| Tool | Purpose |
|---|---|
| ZoomInfo `search_companies` | ICP-filtered company search (Lane A) |
| ZoomInfo `get_recommended_contacts` | Best-fit contacts at a company |
| ZoomInfo `search_intent` | Buying-intent topics (Lane B) |
| ZoomInfo `search_scoops` | Funding / leadership / initiative scoops (Lane B) |
| ZoomInfo `find_similar_companies` | Lookalikes of current customers (Lane C) |
| Prospecting MCP `signals_companies_search` | Secondary intent/signal source (Lane B) |
| Prospecting MCP `lookalike_companies` | Secondary lookalike source (Lane C) |
| Web search | Sanity-check the company/contact exists and is current |

Load via ToolSearch: ZoomInfo `mcp__0de0dfba-...`, prospecting MCP
`mcp__cc7bd256-...`.

## The three lanes (aim ~3–4 candidates each)

### Lane A — ICP fit
Use `search_companies` filtered to the ICP in
`data/icp-freshservice-nordic.md`: Sweden/Finland HQ, target industries,
employee band (sweet spot 250–1000). For each company, use
`get_recommended_contacts` filtered to the contact priority order below.

### Lane B — Buying signals
Use `search_intent` + `search_scoops` (topics: ITSM, ServiceNow
renewal/alternative, Jira Service Management, employee experience, IT
automation) and `signals_companies_search` to find companies showing
intent or relevant scoops (funding, IT/digital leadership hire, vendor
evaluation, reorg/M&A). Keep only Sweden/Finland + ICP-shaped companies.

### Lane C — Lookalikes of current customers
Read `data/nordic-freshservice-customers.md`. For the strongest 3–5 anchor
accounts, call `find_similar_companies` (and `lookalike_companies` as a
secondary source). Filter results to Nordic geo + matching size band.

## Contact priority (one contact per company)

Mirror `data/active-contacts.md`:
- **Priority 1:** Head of IT Service Management / Head of IT Service
  Delivery / Head of IT Operations
- **Priority 2:** IT Director
- **Priority 3:** Head of IT (fallback only)

Pick the highest-priority reachable contact. If none of these exist at a
company, note that and let the user decide.

## Process

1. Run all three lanes.
2. **Dedupe** across lanes (a company found in two lanes is one candidate —
   note both reasons).
3. Cap at ~10 strongest candidates, balanced across lanes where possible.
4. Sanity-check each company and contact is real and current (quick web
   check). Never invent a company or person.

## Output (for review — paste-ready)

A table the user can review and copy from into `data/active-contacts.md`:

```
CANDIDATES — reviewed [DATE]

| # | Company | Country | Contact Name | Title | LinkedIn URL | Lane | Why now |
|---|---------|---------|--------------|-------|--------------|------|---------|
| 1 | ... | ... | ... | ... | ... | A/B/C | one-line rationale 📎 source |
...
```

Plus a one-line note flagging any candidate where the contact tier dropped
to Priority 3 or where data was thin.

## Quality rules

- **Source URL** for the "why now" rationale on every candidate.
- **No fabrication** — real companies, real current contacts only.
- **Respect compliance** — legitimate B2B research, no bulk scraping, no
  mass-list building. Individual research queries only.
- **Do not write to `data/active-contacts.md`.** Output is for the user to
  review and copy manually.
- **On-demand only.** Do not set up a schedule for this skill.
