---
name: google-doc-delivery
description: Delivery phase of the weekly-prospect-outreach routine. Packages the week's pipeline into one Google Doc (New this week / In cadence / Exited this week) plus a short drafted summary email with a pipeline-health header. The weekly summary email is drafted (not sent). Draft-only.
---

# Google Doc Delivery — Freshservice Nordic

## Purpose

Package the week's pipeline into a **two-layer output**:
- **Layer 2 — one Google Doc** in Drive with everything.
- **Layer 1 — one drafted summary email** linking to it, with a pipeline-health
  header.

You are given: companies entering Week 1 (new), companies in Weeks 2–4 with
this week's drafted touches, companies that exited this run, and the run date.

## Tools
| Tool | Purpose |
|---|---|
| Google Drive `mcp__Google_Drive__create_file` | Create the Google Doc (Layer 2) |
| Gmail `mcp__Gmail__create_draft` | Draft the summary email (Layer 1) |

## Layer 2 — the Google Doc

**Title:** `Outreach Pipeline — Week of [DATE]`

Three sections:

### 1. NEW THIS WEEK (entering Week 1)
For each new company — the signal rationale that earned the slot, then a section
per contact (as below).

### 2. IN CADENCE (Weeks 2–4)
For each active company, a section per contact with this week's assets.

**Per company header + per contact block:**
```
─────────────────────────────────────────────
[Company] — [country] — [industry] — [employee band]   | Cadence Week [X] of 4
Qualifying signal: [why they're in the pipeline]        📎 [url]
Incumbent: [tool or unknown]                            📎 [url]
Best-match proof: [customer]

  Contact: [Name] — [Title] — persona: [budget owner/champion/I&O]
  Anchor: [TRIGGER/INCUMBENT/NEWS/ROLE/PROOF] — [why]
  Confidence: [HIGH/MEDIUM/LOW] — [reason]
  Language: [Swedish / English]

  This week's touches (Week [X]):
  [label 1]
  <asset in a fenced block>
  [label 2]
  <asset in a fenced block>
  [label 3]
  <asset in a fenced block>

  Sources: [linked urls]
```

Touch labels per week:
- Week 1: `[EMAIL 1]` / `[LINKEDIN CONNECT]` / `[COLD CALL SCRIPT 1]`
- Week 2: `[LINKEDIN DM]` / `[EMAIL 2]` / `[COLD CALL SCRIPT 2]`
- Week 3: `[VIDEO SCRIPT]` / `[EMAIL 3]` / `[COLD CALL SCRIPT 3]`
- Week 4: `[LINKEDIN ENGAGE]` / `[EMAIL 4]` / `[COLD CALL SCRIPT 4]`

Keep each contact's assets in that contact's language (Swedish / English).

### 3. EXITED THIS WEEK
Table: `Company | Reason (no-response / not-interested / disqualified / responded / drop-company) | Cooldown-until`.

After creating the Doc, capture its shareable URL for Layer 1.

## Layer 1 — the summary email (DRAFT, do not send)

Draft to **mohsen.salimitari@freshworks.com**.

**Subject:** `Outreach Pipeline — Week of [DATE] | [A] active · [N] new · [E] exited`

**Body** — under ~300 words, a navigation aid (no exclamation marks / emoji per
Moe's voice):
```
Hi Moe,

Pipeline for the week of [DATE].
Active: [A] companies · Added this week: [N] · Exited: [E].
Full assets: [LINK TO GOOGLE DOC]

NEW THIS WEEK
• [Company] — [contact count] contacts — Week 1 — signal: [why now]
• ...

IN CADENCE
• [Company] — Week [X] — [contact count] contacts ready
• ...

EXITED
• [Company] — [reason]

FLAGS
• [Any MEDIUM/LOW confidence companies + one-line reason]
• [Any research gaps or size-exceptions]
• [If clean: "No flags this week."]

Reminder: mark responses/disqualifications in data/pipeline.md (Status column).

— Prospect Outreach Routine
```

Then confirm to the user: *"Pipeline Doc created in Drive and summary drafted in
your Gmail — ready for review."*

## Quality rules
- **Summary email is DRAFTED, never sent.** Moe reviews and sends.
- **Every claim in the Doc carries a linked source URL.**
- **Surface confidence flags + size-exceptions** in Layer 1.
- **Match each contact's language** (Swedish for Sweden, English for Finland).
- **One Doc + one summary draft per run.**
