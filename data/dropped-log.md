# Dropped / Exited Companies Log

<!--
  WORKFLOW-OWNED FILE. The weekly-prospect-outreach routine appends a row here
  whenever a company exits the pipeline, and reads it to enforce cooldowns so a
  freshly-declined company is not immediately re-sourced.

  Only WHOLE-ACCOUNT exits are logged here. Removing a single contact (while the
  company still has active contacts) is NOT an exit and is NOT logged — the
  company stays in the pipeline and out of cooldown.

  Reasons:
    no-response     → completed the 4-week / 12-touch cadence with no reply.
                      Cooldown = cooldown_weeks_no_response (data/config.md).
    not-interested  → the whole account exited because all its contacts declined
                      / were disqualified. Cooldown = cooldown_weeks_not_interested.
    meeting-booked  → a contact booked a meeting; account paused/engaged and
                      handed to the AE. Cooldown = cooldown_weeks_meeting_or_engaged.
    drop-company    → manually exited via the pipeline Status column.

  Cooldown-until is a date; identification skips any company listed here whose
  Cooldown-until has not yet passed. You may hand-clear a row if you want a
  company reconsidered sooner.
-->

| Company | Reason | Exited | Cooldown-until |
|---------|--------|--------|----------------|
