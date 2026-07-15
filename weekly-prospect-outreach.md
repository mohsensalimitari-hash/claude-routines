---
name: weekly-prospect-outreach
description: Always-on weekly outreach engine for a Freshworks Nordic Lead AE. Runs a living pipeline of Sweden/Finland companies, each on its own 4-week / 12-touch cadence. Every Monday it removes declined/aged-out companies, advances everyone one week, sources up to a capped number of net-new signal-qualified companies (3–4 contacts each), researches and drafts that week's touches, and delivers a Google Doc + drafted summary email. Triggers on "weekly outreach", "Monday outreach", "prospect outreach run", "run the pipeline", or scheduled Remote Routine invocation.
---

# Weekly Prospect Outreach — Freshworks Nordic (living pipeline)

## Operating Context

You support a **Lead Account Executive at Freshworks** (Mohsen "Moe" Salimi
Tari) covering **Sweden and Finland**, selling **Freshservice** (ITSM / EX).

This is an **always-on pipeline**, not a fixed cohort. Each company runs its own
**4-week / 12-touch cadence** (three touches per week). Every weekly run the
pipeline **self-cleans** (removes declined / aged-out companies), **advances**
everyone one week, and **adds** up to a capped number of net-new, signal-
qualified companies. It sources its own targets — you do not maintain a contact
list; your only manual input is the `Status` column in `data/pipeline.md`.

## Inputs / state

| File | Role |
|---|---|
| `data/pipeline.md` | Living pipeline (workflow-owned; you edit only `Status`) |
| `data/config.md` | Caps, cooldowns, signal threshold |
| `data/dropped-log.md` | Exited companies + cooldowns |
| `data/icp-freshservice-nordic.md` | ICP (from the Partner Playbook; Nordic-calibrated) |
| `data/nordic-freshservice-customers.md` | Proof points + lookalike anchors + exclusions |
| `moe-writing-styles.md` | Moe's voice for all drafted copy |

## Sub-skills

| Skill | Phase |
|---|---|
| `skills/prospect-identification.md` | Source net-new companies (signal-ranked) |
| `skills/prospect-research.md` | Dossier per company |
| `skills/outreach-drafter.md` | This week's 3 touches per contact |
| `skills/google-doc-delivery.md` | Google Doc + drafted summary email |

---

## Execution Flow

### Step 1 — Load
Read `data/pipeline.md`, `data/config.md`, `data/dropped-log.md`.

### Step 2 — Status reconciliation (mid-cadence exits, runs first)
For each pipeline row, read the per-contact `Status`:
- Any value other than `active` → remove **that contact**; append to
  `data/dropped-log.md` with its reason (`responded` / `not-interested` /
  `disqualified`) and cooldown per `config.md`.
- `drop-company` on any row, **or** a company whose every contact is now
  inactive → exit the **whole account** to `dropped-log.md`.
This runs regardless of cadence week — a week-2 "not interested" drops immediately.

### Step 3 — Advance cadence
For each still-active company, increment `Cadence Week`. Any company that just
moved **past Week 4** (all 12 touches delivered) with no response → exit to
`dropped-log.md` (reason `no-response`, cooldown `cooldown_weeks_no_response`).

### Step 4 — Add net-new companies
Invoke `skills/prospect-identification.md`. Add up to `weekly_add_cap`
(or `first_run_seed_cap` if the pipeline is empty) companies scoring
**≥ `signal_score_threshold`**. Dedupe against the current pipeline, existing
customers (`nordic-freshservice-customers.md`), and anything in cooldown in
`dropped-log.md`. **Adding fewer than the cap is correct** when few qualify.
New companies enter at **Cadence Week 1**, Status `active`, with 3–4 contacts,
carrying their company signal / contact signal / sources into the pipeline rows.

### Step 5 — Research
For each company that has touches due this week (Weeks 1–4), invoke
`skills/prospect-research.md` (company researched once; multi-source: ZoomInfo +
Lusha + public web — news, annual/quarterly reports, interviews, trends).

### Step 6 — Draft
For each active contact, invoke `skills/outreach-drafter.md` with the company
dossier + that company's `Cadence Week` → that week's three touches. Voice per
`moe-writing-styles.md`. **Swedish** for Sweden contacts, **English** for Finland.

### Step 7 — Deliver
Invoke `skills/google-doc-delivery.md` once with: new companies, in-cadence
companies + drafted touches, exited companies, and the run date. It creates one
Google Doc (New / In-cadence / Exited) and **drafts** the summary email to
`mohsen.salimitari@freshworks.com`. Confirm to the user when both exist.

### Step 8 — Persist
Write the updated `data/pipeline.md` (new companies added, weeks incremented,
exited rows removed) and `data/dropped-log.md`. The pipeline runs continuously —
there is no cycle-completion pause.

---

## Guardrails

- **Draft-only.** Nothing outbound is sent or posted on Moe's behalf. Emails,
  LinkedIn notes/DMs, call scripts, and video scripts are all drafts/assets. The
  only email created is the **drafted** weekly summary to Moe's own address.
- **Source URL on every signal and factual claim**; "unknown" allowed; no
  fabricated companies, contacts, news, or metrics.
- **Only real playbook proof figures** in outreach — never invented numbers.
- **Dedupe + cooldown** always enforced (pipeline + customers + dropped-log).
- **Size discipline:** 1,000–5,000 is the primary band; outside it only on very
  strong funded intent (flagged as an exception).
- **HaloITSM** is treated as a live Nordic competitor (the playbook's TOPdesk
  emphasis is partly a Netherlands-data artifact).
- Legitimate B2B research only (ZoomInfo/Lusha terms); GDPR legitimate-interest —
  draft-only keeps a human in the loop before any contact.

## Scheduling Guidance

Set up as a **Claude Code Remote Routine** (not a Cowork scheduled task).
- **Trigger:** every **Monday at 07:30** local time.
- **Routine prompt:** *"Run the weekly prospect outreach pipeline."*

The pipeline self-manages cadence and membership via `data/pipeline.md`, so the
schedule never changes. `prospect-identification.md` at the repo root is the
**on-demand, review-only** entry point and is never scheduled.

## Connectors Used

| Connector | Purpose |
|---|---|
| **ZoomInfo** | Firmographics, intent, scoops, company signals, contacts |
| **Lusha** | Contact gaps, decision-makers, company signals, website visits |
| **Web search / fetch** | News, annual/quarterly reports, interviews, sector trends |
| **Google Drive** | Creates the weekly pipeline Google Doc |
| **Gmail** | Drafts the weekly summary email |
