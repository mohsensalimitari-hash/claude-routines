---
name: google-doc-delivery
description: Phase 3 of the weekly-prospect-outreach routine. Packages the week's dossiers and drafted touches into a two-layer output — one Google Doc in Drive (full assets per contact) plus a short navigation summary email drafted to Gmail. Replaces the old single-digest delivery. The weekly summary email is drafted (not sent); the cycle-completion email is handled by the orchestrator.
---

# Google Doc Delivery — Freshworks Nordic

## Purpose

Take the full set of per-contact dossiers + this week's drafted touches and
produce a **two-layer output**:

- **Layer 2 — one Google Doc** in Drive holding everything.
- **Layer 1 — one short summary email** drafted to Gmail, linking to the Doc.

You are told `current_run` / week number (1–4) and the run date.

## Tools

| Tool | Purpose |
|---|---|
| Google Drive `create_file` | Create the Google Doc (Layer 2) |
| Gmail `create_draft` | Draft the summary email (Layer 1) |

Load via ToolSearch: Drive tools are prefixed `mcp__66cc2eb3-...`,
Gmail tools `mcp__7618e3c8-...`.

## Layer 2 — the Google Doc

**Title:** `Outreach Week [X] of 4 — [DATE]`

**Structure:** one section per contact, in the order they appear in
`data/active-contacts.md`. Each section:

```
─────────────────────────────────────────────
[N]. [Company] — [Contact Name], [Title]   (Lane week X of 4)
─────────────────────────────────────────────

COMPANY SNAPSHOT
• [what they do]
• [size]
• [relevant recent news]
• [tech-stack signal]

CONTACT SNAPSHOT
• Title / seniority: [...]
• Tenure: [... or unknown]
• Signals: [... or none found]

PERSONALISATION ANCHOR: [NEWS | ROLE | TECH | PROOF]
Why: [one line]

THIS WEEK'S TOUCHES
[EMAIL 1]            (labels change per week — see drafter)
<the email subject + body in a fenced block>

[LINKEDIN CONNECT]
<the connect note in a fenced block>

[COLD CALL SCRIPT 1]
<the script in a fenced block>

SOURCES
• [linked URL]
• [linked URL]

CONFIDENCE: [HIGH | MEDIUM | LOW] — [one-line reason]
```

The three touch labels change with the week:
- Week 1: `[EMAIL 1]` / `[LINKEDIN CONNECT]` / `[COLD CALL SCRIPT 1]`
- Week 2: `[LINKEDIN DM]` / `[EMAIL 2]` / `[COLD CALL SCRIPT 2]`
- Week 3: `[VIDEO SCRIPT]` / `[EMAIL 3]` / `[COLD CALL SCRIPT 3]`
- Week 4: `[LINKEDIN ENGAGE]` / `[EMAIL 4]` / `[COLD CALL SCRIPT 4]`

Put every email/script/note inside a clearly delimited block so it is
trivial to copy. Keep each contact's touches in the contact's outreach
language (Swedish / English).

After creating the Doc, capture its shareable URL — Layer 1 links to it.

## Layer 1 — the summary email (DRAFT, do not send)

Draft to: **mohsen.salimitari@freshworks.com**

**Subject:** `🎯 Outreach Week [X] of 4 — [DATE] | 10 Contacts Ready`

**Body** — keep under 300 words. This is a navigation aid, not the content:

```
Hi Moe,

Week [X] of 4 is ready. Full assets are in the Google Doc:
[LINK TO GOOGLE DOC]

This week's touches per contact: [name the 3 touch types for week X].

CONTACTS
1. [Company] — [Name], [Title] — Week [X] touches ready — Anchor: [basis]
2. ...
... (one line per contact, all 10)

RESEARCH NOTES
• [Any contacts flagged MEDIUM/LOW confidence, with the one-line reason]
• [Any gaps — e.g. "Contact 7: tenure unknown, anchor is generic"]
• [If all clean: "All 10 researched cleanly — no flags this week."]

— Prospect Outreach Routine
```

Then confirm to the user: *"Week [X] Doc created in Drive and summary
drafted in your Gmail — ready for review."*

## Quality rules

- **Summary email is DRAFTED, never sent.** The user reviews and sends.
- **Every claim in the Doc has a linked source URL.** No URL → it does not
  go in as fact.
- **Surface confidence flags honestly** in Layer 1 so the user knows which
  contacts to sanity-check before acting.
- **Match outreach language per contact** (Swedish for Sweden, English for
  Finland) — the Doc and the copied assets must reflect that.
- **One Doc, one summary draft per run.** Do not fragment.
