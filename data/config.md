# Pipeline Configuration

<!--
  Tuning knobs for the weekly-prospect-outreach pipeline. Edit these values to
  control how fast the pipeline grows and how long declined companies stay out.
  The pipeline itself (companies + contacts + cadence state) lives in
  data/pipeline.md — do not put companies here.

  Format: `key: value`, one per line. Keep the keys exactly as named.
-->

weekly_add_cap: 10
first_run_seed_cap: 20
cooldown_weeks_no_response: 8
cooldown_weeks_not_interested: 26
signal_score_threshold: 60

<!--
  Field notes:
  - weekly_add_cap: max NET-NEW companies added per weekly run (steady state).
    Fewer is fine — only companies above signal_score_threshold get added.
  - first_run_seed_cap: max companies to add on the very first run when the
    pipeline is empty.
  - cooldown_weeks_no_response: weeks a company that aged out with no response
    is skipped before it can be re-sourced.
  - cooldown_weeks_not_interested: longer cooldown for companies/contacts that
    explicitly declined or were disqualified.
  - signal_score_threshold: 0–100. Minimum company score (see
    skills/prospect-identification.md) required to enter the pipeline. Raise it
    to be more selective, lower it to add more. Tune during the sourcing dry-run.
-->
