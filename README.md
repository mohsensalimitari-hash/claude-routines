# claude-routines

Claude Code routines and skills for a Freshworks Nordic Lead AE (Sweden &
Finland, selling Freshservice — ITSM/EX).

## Routines (repo root)

- **`competitor-weekly-brief.md`** — weekly competitive-intel digest drafted to
  Gmail (HaloITSM, ServiceNow, Atlassian, Zendesk, Salesforce).
- **`weekly-prospect-outreach.md`** — always-on outreach pipeline. Each Monday it
  self-cleans, advances every company one week of a 4-week / 12-touch cadence,
  sources up to a capped number of net-new signal-qualified companies (3–4
  contacts each), researches + drafts that week's touches, and delivers a Google
  Doc + drafted summary email. Sources its own targets — draft-only.
- **`prospect-identification.md`** — on-demand, review-only prospecting. Prints
  ranked candidates without touching the live pipeline.

## Skills (`skills/`)

Called by the outreach routine: `prospect-identification` (sourcing engine),
`prospect-research` (per-company dossier), `outreach-drafter` (12-touch cadence
copy in Moe's voice), `google-doc-delivery` (Doc + summary email).

## Data (`data/`)

- `pipeline.md` — living pipeline (workflow-owned; you edit only the `Status` column).
- `config.md` — caps, cooldowns, signal threshold.
- `dropped-log.md` — exited companies + cooldowns.
- `icp-freshservice-nordic.md` — ICP distilled from the Partner Playbook (Nordic-calibrated).
- `nordic-freshservice-customers.md` — reference customers for proof + lookalikes.

## Other

- `moe-writing-styles.md` — Moe's writing voice (used by the outreach drafter).
- `docs/` — source material (Freshservice Nordic Partner Playbook).
