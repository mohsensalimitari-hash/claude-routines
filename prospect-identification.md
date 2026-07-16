---
name: prospect-identification
description: On-demand prospecting for Freshservice Nordic. Surfaces net-new Sweden/Finland companies (Partner Playbook ICP) with 3–4 ranked contacts each, ranked by publicly-available buying signals, for you to review. Triggers on "find new prospects", "surface candidates", "who should I target", "prospect identification". Review-only — it does NOT touch the live pipeline; the weekly-prospect-outreach routine handles pipeline additions automatically. Run this when you want to eyeball fresh candidates or sanity-check the sourcing.
---

# Prospect Identification — On-Demand (review-only)

## Purpose

A manual, **review-only** entry point to the sourcing engine. Use it when you
want to see fresh candidates on demand — e.g. to sanity-check what the weekly
routine would pull, or to eyeball a particular slice of the territory.

**It does not mutate `data/pipeline.md`.** The weekly `weekly-prospect-outreach`
routine is what actually adds companies to the pipeline (capped and deduped).
This wrapper only prints a ranked list for your eyes.

## What it does

1. Invoke the shared engine `skills/prospect-identification.md`.
2. Apply the same exclusions (existing customers, current pipeline, cooldown
   log) so you don't see anyone already being worked.
3. Print the ranked companies + contacts table (engine output format), newest
   signals first.
4. Stop. Make **no** writes to pipeline, config, or logs.

## Optional focus

If you pass a focus (e.g. "manufacturing in Finland", "companies showing
ServiceNow-migration intent", "lookalikes of Plantagen"), narrow the engine's
lanes accordingly but keep all ICP guardrails from
`data/icp-freshservice-nordic.md` in force.

## Scheduling

**None — on demand only.** This skill is never scheduled. Weekly automated
sourcing happens inside `weekly-prospect-outreach`.

## Guardrails

Same as the engine: real companies/contacts only, source URL on every signal,
"unknown" allowed, dedupe + cooldown respected, **HQ in Sweden/Finland (hard
filter)**, 1,000–5,000 primary size band (outside only on very strong funded
intent), HaloITSM weighted as a live Nordic competitor, legitimate B2B research
only. Incumbent tool is detected via ZoomInfo tech-product tags (engine Lane D)
where possible. When ZoomInfo + Lusha return thin IT-leadership contacts for a
company worth pursuing, the engine flags `contactability` and prints a ready
**LinkedIn Sales Navigator** search for you to check manually — it never scrapes
LinkedIn itself.
