# Pipeline Configuration

<!--
  Tuning knobs for the weekly-prospect-outreach pipeline. Edit these values to
  control how fast the pipeline grows and how long declined companies stay out.
  The pipeline itself (companies + contacts + cadence state) lives in
  data/pipeline.md — do not put companies here.

  Format: `key: value`, one per line. Keep the keys exactly as named.
-->

weekly_add_cap: 5
first_run_seed_cap: 20
max_active_companies: 20
cooldown_weeks_no_response: 8
cooldown_weeks_not_interested: 26
cooldown_weeks_meeting_or_engaged: 52
signal_score_threshold: 60

<!--
  Field notes:
  - weekly_add_cap: max NET-NEW companies added per weekly run (steady state).
    Fewer is fine — only companies above signal_score_threshold get added.
  - first_run_seed_cap: max companies to add on the very first run when the
    pipeline is empty.
  - max_active_companies: HARD CEILING on the active pipeline. New companies are
    added only while the active count is below this. Because the first run seeds
    up to this ceiling (20), weekly adds pause for ~3 weeks until that initial
    cohort ages out at Week 4, then resume at up to weekly_add_cap/week. Steady
    state ≈ 20 active companies (~70 contacts). Raise only if a BDR shares load.
  - cooldown_weeks_no_response: weeks a company that aged out (completed the
    4-week cadence with no response) is skipped before it can be re-sourced.
  - cooldown_weeks_not_interested: longer cooldown for a company that fully
    exited because its contacts declined / were disqualified.
  - cooldown_weeks_meeting_or_engaged: a company paused because a contact booked
    a meeting is not re-sourced for this long (~1 year). The AE owns it manually.
  - signal_score_threshold: 0–100. Minimum company score (see
    skills/prospect-identification.md) required to enter the pipeline. Raise it
    to be more selective, lower it to add more. Tune during the sourcing dry-run.
-->
