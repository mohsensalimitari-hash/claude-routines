# Outreach Pipeline (living state)

<!--
  ═══════════════════════════════════════════════════════════════════════════
  WORKFLOW-OWNED FILE. The weekly-prospect-outreach routine reads and rewrites
  this file every run. It is the single source of truth for who is in the
  cadence and what week each company is on.

  YOU EDIT ONLY THE `Status` COLUMN. Everything else is managed automatically;
  any manual edit to other columns will be overwritten on the next run.
  ═══════════════════════════════════════════════════════════════════════════

  HOW TO USE THE Status COLUMN (per contact row):
    active         → keep contacting on the normal cadence (default). A plain
                     reply that is NOT a booked meeting and NOT a disqualifier
                     does not stop anything — leave it active; the cadence keeps
                     running until a meeting or a disqualifier.
    meeting-booked → you secured a meeting with this contact → PAUSE THE WHOLE
                     ACCOUNT. All contacts at the company stop; the account moves
                     to an engaged state and is not re-sourced (~1 year). You own
                     it manually from here. This is the ONLY status that pauses
                     the other contacts at the same company.
    not-interested → this contact declined / is happy with their current vendor /
                     just renewed / just purchased → remove THIS contact only.
                     The company keeps going as long as ≥1 contact stays active.
    disqualified   → you ruled this contact out (e.g. on a cold call) → remove
                     THIS contact only (same behaviour as not-interested).
    drop-company   → exit the WHOLE account immediately (put on any one row).
                     Use when the company as a whole is not a fit.

  A contact marked not-interested / disqualified is removed at the next run. The
  company exits ONLY when: you use drop-company, OR every contact has become
  inactive, OR a contact is meeting-booked (whole-account pause), OR it ages out
  past Week 4 with no response. Removing one contact does NOT drop the company
  and does NOT put it in cooldown. Removal happens at whatever cadence week the
  account is on — mid-cadence exits (e.g. a week-2 "not interested") are normal
  and immediate.

  COLUMN MEANINGS (workflow-managed):
    Cadence Week    1–4, incremented each weekly run. All contacts at a company
                    share the same week.
    Added           date the company entered the pipeline (Week 1).
    Company Signal  the qualifying "why now" that earned the slot (with source).
    Contact Signal  the per-contact reason (job change, seniority fit, etc.).
    Sources         URLs backing the signals.

  The pipeline starts empty. The first run seeds it (up to first_run_seed_cap in
  data/config.md).
-->

| Company | Country | Cadence Week | Added | Status | Contact | Title | LinkedIn | Company Signal | Contact Signal | Sources |
|---------|---------|--------------|-------|--------|---------|-------|----------|----------------|----------------|---------|
