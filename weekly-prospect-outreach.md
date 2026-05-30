---
name: weekly-prospect-outreach
description: Weekly outreach engine for a Freshworks Nordic Lead AE. Every Monday it works the same 10 contacts (maintained by hand in data/active-contacts.md) through a 12-touch / 4-week cadence, advancing one week per run. For the current week it researches each contact, drafts that week's 3 outreach touches (email / LinkedIn / cold call / video), and delivers a Google Doc plus a summary email. After 4 runs it pauses and asks for a contact-list refresh. Triggers on "weekly outreach", "Monday outreach", "prospect outreach run", "run the outreach routine", or scheduled Remote Routine invocation. Does NOT find prospects — for that, use the standalone prospect-identification skill.
---

# Weekly Prospect Outreach — Freshworks Nordic

## Operating Context

You support a **Lead Account Executive at Freshworks** covering **Sweden
and Finland**, selling **Freshservice** (ITSM / Employee Experience).

This routine does **not** find prospects. It works a fixed set of **10
contacts** that the user maintains by hand in `data/active-contacts.md`,
progressing each contact through a **12-touch, 4-week cadence** — three
touches per week. It runs every Monday and advances exactly one week per
run. After the 4th run it pauses for a contact refresh.

To source new candidates, the user runs the separate, on-demand
`prospect-identification` skill — that is not part of this routine.

## Inputs it reads

| File | Role |
|---|---|
| `data/cycle-state.md` | `current_run` (1–4) and `status` — auto-managed by this routine |
| `data/active-contacts.md` | The 10 contacts (user-maintained) |
| `data/nordic-freshservice-customers.md` | Proof points for Week 2 DM + Week 3 email |

## Sub-skills it invokes

| Skill | Phase |
|---|---|
| `skills/prospect-research.md` | 1 — dossier per contact |
| `skills/outreach-drafter.md` | 2 — the current week's 3 touches per contact |
| `skills/google-doc-delivery.md` | 3 — Google Doc + drafted summary email |

---

## Execution Flow

### Step 0 — State gate
Read `data/cycle-state.md`.
- If `status: awaiting-reset` → **STOP**. Tell the user:
  *"The 4-week cycle is complete and waiting for a reset. Update
  data/active-contacts.md with 10 new contacts, then set current_run: 1 and
  status: active in data/cycle-state.md to start a new cycle."* Do nothing
  else.
- Otherwise read `current_run` (expect 1, 2, 3 or 4). This is **Week X**.

### Step 1 — Load contacts
Read `data/active-contacts.md`. Parse the 10 rows
(`Company, Country, Contact Name, Title, LinkedIn URL`). Skip blank rows.
If fewer than 10 populated rows, proceed with what's there but warn the
user how many were found. If zero, stop and tell the user to populate the
file.

### Step 2 — Phase 1: Research
For each contact, invoke `skills/prospect-research.md`, passing the contact
row and `current_run`. Collect one dossier per contact. (Run 1 = full
research; runs 2–4 = lighter refresh confirming the anchor + new
developments.)

### Step 3 — Phase 2: Draft
For each dossier, invoke `skills/outreach-drafter.md`, passing the dossier
and `current_run`. It returns **only Week X's three touches**. Language is
Swedish for Sweden contacts, English for Finland contacts — never mixed.

### Step 4 — Phase 3: Deliver
Invoke `skills/google-doc-delivery.md` once with all dossiers + drafted
touches + `current_run` + run date. It creates one Google Doc (all
contacts, Week X assets) and **drafts** the Layer-1 summary email to
`mohsen.salimitari@freshworks.com`. Confirm to the user when both exist.

### Step 5 — Advance the cycle
Increment `current_run` in `data/cycle-state.md` by 1.

### Step 6 — Cycle completion (only if the run just completed was run 4)
When the run that just finished was **run 4**:
1. **Auto-send** an email (this one is sent, not drafted) to
   `mohsen.salimitari@freshworks.com`:
   - Subject: `Outreach Cycle Complete — Please Update Your Contact List`
   - Body: confirm the 4-week cycle is done; instruct the user to replace
     all 10 rows in `data/active-contacts.md` with 10 new companies/
     contacts; and to manually reset `current_run` to `1` in
     `data/cycle-state.md` when ready to start a new cycle.
2. Set `status: awaiting-reset` in `data/cycle-state.md`. (After
   incrementing in Step 5, `current_run` will read 5 — that's fine; the
   manual reset returns it to 1. The `awaiting-reset` status is the gate.)
3. Do **not** run again until the user manually sets `status: active`.

---

## Email-delivery policy (locked)

- **Weekly summary email** (Step 4, via google-doc-delivery): **DRAFTED**
  for review. Never auto-sent.
- **Cycle-completion email** (Step 6): **AUTO-SENT**, so the reset prompt
  cannot be missed.
- No outreach is ever sent or posted on the user's behalf. Emails, LinkedIn
  notes/DMs, call scripts, and video scripts are all drafts/assets for the
  user to dispatch from their own stack.

## Quality Rules

- **Source URL on every factual claim** in research and the Doc.
- **No fabricated contacts, companies, news, or contact details.**
- **"Unknown" is allowed** — flag gaps honestly via confidence flags.
- **Respect the cadence and language rules** exactly.
- **One Google Doc + one summary draft per run.** No fragmentation.
- **Honour the state gate** — never run outreach while `awaiting-reset`.

## Scheduling Guidance

Set this up as a **Claude Code Remote Routine** (not a Cowork scheduled
task).

- **Trigger:** every **Monday at 07:30** local time.
- **Routine prompt:** *"Run the weekly prospect outreach routine."*

The routine self-manages which week it is via `data/cycle-state.md`, so the
schedule never changes — it just fires weekly and the routine does the
right thing for the current run (or pauses if `awaiting-reset`).

The standalone `prospect-identification` skill is **on-demand only** and is
never scheduled.

## Connectors Used

| Connector | Purpose |
|---|---|
| **ZoomInfo** | Firmographics, scoops, intent, contact data (research phase) |
| **Web search** | Recent company news and public contact signals |
| **Google Drive** | Creates the weekly Google Doc |
| **Gmail** | Drafts the weekly summary; auto-sends the completion email |
