# Dropped / Exited Companies Log

<!--
  WORKFLOW-OWNED FILE. The weekly-prospect-outreach routine appends a row here
  whenever a company exits the pipeline, and reads it to enforce cooldowns so a
  freshly-declined company is not immediately re-sourced.

  Reasons:
    no-response     → completed the 4-week / 12-touch cadence with no reply.
                      Cooldown = cooldown_weeks_no_response (data/config.md).
    responded       → a contact replied and the account was reviewed out.
    not-interested  → explicit decline. Cooldown = cooldown_weeks_not_interested.
    disqualified    → you ruled it out (e.g. on a cold call). Longer cooldown.
    drop-company    → manually exited via the pipeline Status column.

  Cooldown-until is a date; identification skips any company listed here whose
  Cooldown-until has not yet passed. You may hand-clear a row if you want a
  company reconsidered sooner.
-->

| Company | Reason | Exited | Cooldown-until |
|---------|--------|--------|----------------|
