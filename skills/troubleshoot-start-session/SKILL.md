---
name: troubleshoot-start-session
description: >
  Catch up at the start of a new AI conversation on an existing troubleshooting
  project. Use when starting a fresh session and you need to get the bot up to
  speed on what has been done so far. Reads the project's .ai folder notes,
  asks targeted catch-up questions, updates the notes, and reports findings plus
  recommended next steps. Use when the user says "start a new session,"
  "catch up on this investigation," "troubleshoot-start-session," or similar.
---

## Overview

This skill onboards a fresh AI conversation onto an existing troubleshooting project. It reads the standard `.ai` notes, asks the user what's changed since the notes were last written, re-reads as needed, updates the notes, and reports a concise summary with recommended next steps.

Goal: get oriented fast and conserve context. Do **not** read everything — skip dead-end branches.

## When to invoke

- User starts a new conversation and wants the bot caught up on prior work
- User says "start a new session," "catch up on this investigation," "troubleshoot-start-session," or similar
- The project already has a `.ai/` folder (if not, use `troubleshoot-init` instead)

## Steps

### 1. Get the project folder

Ask for the location of the troubleshooting folder (use `ask_user`). The `.ai/` subfolder lives inside it. If the user already gave the path, skip the question.

Confirm a `.ai/` folder exists. If it doesn't, tell the user and suggest `troubleshoot-init`.

### 2. First pass — read notes 00–06

Read these `.ai` notes to build context:

- `00-tracking.md` — overview, open loops, open questions, current hypothesis
- `01-initial-findings.md` — problem statement, symptoms, timeline
- `02-hypotheses.md` — POCs (possible causes), leading hypothesis
- `03-root-cause-analysis.md` — root cause work
- `04-trace-analysis.md` — trace/log analysis
- `05-next-steps.md` — prioritized next steps, open questions
- `06-assumptions.md` — assumptions made during analysis and their verification status

Read them in a single batch where possible. Some may be skeletons — that's fine.

### 3. Ask catch-up questions

Use `ask_user` for each (one question at a time):

1. **What has been done since the files were last updated?**
2. **Is there anything else you need to know?** (i.e., anything the notes don't capture)
3. **Are there any logs or error messages I should be aware of?**

### 4. Second pass — re-read and read selectively

Re-read notes 00–06 with the user's answers in mind. Read **any other** notes (07+, attachments) only if they're clearly relevant to the open loops or the user's update.

**Be disciplined about context:**
- You do **not** have to read every file.
- Branches are often dead-ends. Don't fill context with notes that no longer matter.
- If a note is stale or superseded, skim or skip it.

### 5. Update the notes

Apply the `.ai` folder conventions from the user's global instructions:

- Update `00-tracking.md`: refresh current hypothesis, open loops, open questions, and the changelog. Keep the **Reference & Locations** section current — add any paths, endpoints, URLs, or IDs the user mentions so they don't have to re-enter them next session. Keep it a roadmap — links and brief descriptions, not detail.
- Add detail to the relevant numbered note (or a new numbered note 07+) capturing what's changed since last session.
- **Log assumptions in `06-assumptions.md`.** Whenever you notice you're making an assumption (in a hypothesis, root-cause reasoning, etc.), write it there. Don't go out of your way to exhaustively enumerate them — just capture the ones you're consciously aware of. A bad assumption is a sneaky failure mode; this doc is what the user reviews to catch them, and what we double-check when we run out of other leads.
- Every new section/edit gets a date and model name. Sign edits with `[Date] - [Model Name] - [Description of Edit]`.
- `poc` = **possible cause**.

**Editing the .ai folder — it's okay to edit:**
- Notes **00–06 are living documents.** Edit, reorganize, and re-label them to stay readable and useful.
- **Never delete information that may be useful later** — especially hypotheses and assumptions. A wrong/busted hypothesis stays: knowing it's wrong *and why* is valuable. Mark it busted, don't remove it.
- If a doc gets bloated with large chunks you want out of the way, **offload them to another doc in the `.ai` folder** rather than deleting.
- The other docs (07+) are closer to **logs** than 00–06. They're still editable — just don't delete and lose info that may matter later.

### 6. Report

Tell the user, clearly and concisely (clarity over grammar; terse is fine):

- **Where things stand** — current leading hypothesis and what's been confirmed/ruled out
- **What changed** since the notes were last updated (from their answers)
- **Recommended next steps** — prioritized, actionable, drawn from `05-next-steps.md` and the new context
- **Assumptions worth checking** — flag any unverified assumptions from `06-assumptions.md` that could matter
- **Open questions** still unanswered

Keep it short. Conveying the information clearly and concisely matters more than completeness or grammar.

## Important rules (from global instructions)

- `poc` means **possible cause**, not proof of concept.
- Each note/section gets a date and model name.
- **Notes 00–06 are living documents** — edit, reorganize, and re-label freely to keep them readable. Docs 07+ are log-like but also editable.
- **Log assumptions in `06-assumptions.md`** as you become aware of making them. The user reviews this doc to catch bad assumptions; it's also where we look when we run out of other leads.
- **Never delete useful info.** Keep busted hypotheses and assumptions (mark them wrong + why). Offload large unwanted chunks to another `.ai` doc instead of deleting. Sign edits: `[Date] - [Model Name] - [Description]`.
- Keep `00-tracking.md` as a roadmap (TOC + open loops + brief descriptions), not detailed content.
- Don't read files unnecessarily — conserve context, skip dead-end branches.
