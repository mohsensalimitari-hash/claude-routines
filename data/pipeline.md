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
    active         → keep contacting on the normal cadence (default)
    responded      → they replied; pause outreach and review. Contact is removed
                     from the active cadence (logged as a response).
    not-interested → they said no. Contact removed; company gets the longer
                     cooldown before it can be re-sourced.
    disqualified   → you ruled them out (e.g. on a cold call). Contact removed.
    drop-company   → exit the WHOLE account immediately (put on any one of its
                     rows). Use when the company as a whole is not a fit.

  Any value other than `active` removes THAT contact at the next run. If every
  contact at a company becomes inactive (or you use drop-company), the whole
  company exits to data/dropped-log.md. Removal happens at whatever cadence week
  the account is on — mid-cadence exits (e.g. a week-2 "not interested") are
  normal and immediate.

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
