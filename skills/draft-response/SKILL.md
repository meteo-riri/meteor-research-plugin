---
name: draft-response
description: >
  Draft a point-by-point reviewer response letter for a scientific manuscript.
  Triggers: reviewer comments pasted into the conversation, "respond to reviewers",
  "write a rebuttal", "draft response letter", "리뷰어 답변 초안 작성해줘",
  "심사 의견에 답변해줘", "reviewer response 써줘", "rebuttal letter 써줘".
allowed-tools: Read, Glob, WebSearch, WebFetch, Write, Agent
---

# draft-response

## Step 0 — Load mandatory policy

Read `${CLAUDE_PLUGIN_ROOT}/references/anti-fabrication.md`.
Also read `<skill-dir>/references/safety-rules.md`.

If either file cannot be loaded, STOP and report:
> "Mandatory policy files could not be loaded. Reviewer response drafting aborted."

Do NOT proceed until both are confirmed loaded.

---

## Step 1 — Find input files

Search the current working directory for these patterns:
- Manuscript: `manuscript*.md`, `manuscript*.txt`, `paper*.md`, `paper*.txt`
- Reviewer comments: `reviewer*.txt`, `review*.txt`, `comments*.txt`, `reviewer*.md`
- Previous drafts: `response*.md`, `rebuttal*.md`

**Selection rules:**
- If multiple manuscript candidates found: list them, ask user to choose. Do NOT auto-select.
- If multiple reviewer comment candidates found: list them, ask user to choose. Do NOT auto-select.
- If exactly one manuscript and one reviewer file found: show filenames to user and ask to confirm before reading.
- `response*.md` files are previous drafts — do NOT use as manuscript input. Distinguish them.

**DOCX / PDF handling:**
- Attempt to read `.docx` or `.pdf` if found
- If read fails: `Could not read {filename} — please export as .md or .txt and try again`
- Do NOT infer content from a failed read

**If no files found:** Ask the user to either:
1. Paste reviewer comments and manuscript context directly, or
2. Save them as `.txt` or `.md` files in the current directory

---

## Step 2 — Parse reviewer comments

Extract the structure:
- Number of reviewers
- Number of comments per reviewer
- Whether reviewer designated Major / Minor (or "Not specified" if absent)

For each comment:
- Record verbatim comment text
- Assign: `Reviewer-designated: Major | Minor | Not specified`
- Assign: `Assistant triage: High | Medium | Low effort` (your judgment, clearly labeled as AI assessment)

---

## Step 3 — Load templates and guides

Load:
- `<skill-dir>/references/response-template.md`
- `<skill-dir>/references/science-writing.md`
- `<skill-dir>/references/reference-rules.md`

---

## Step 4 — Draft responses (parallel agents)

For each reviewer section (up to 3 reviewers in parallel):
- Delegate to `meteor-research:response-drafter`
- Pass: reviewer number, their comments, and ONLY the relevant manuscript sections (not the full manuscript repeatedly)

**Do NOT spawn one agent per comment.** Spawn one agent per reviewer.
If more than 3 reviewers, process in batches.

Wait for all agents to complete, then consolidate.

---

## Step 5 — Consolidate and quality check

After receiving drafts:
1. Resolve any conflicts between reviewer responses (e.g., Reviewer 1 and 2 making contradictory requests)
2. Ensure every response section ends with a Revision status block
3. Verify no fabricated experimental claims slipped through (re-check safety-rules)
4. Replace any missing information with appropriate placeholders
5. Check all proposed citations are marked `[PROPOSED CITATION — AUTHOR APPROVAL REQUIRED]`

---

## Step 6 — Format and save

Assemble the full letter using the template from `response-template.md`.

**File naming:**
- First run: `reviewer-response-v1.md`
- If `reviewer-response-v1.md` exists: use `reviewer-response-v2.md`
- If that exists: use `-v3`, `-v4`, etc.
- **NEVER overwrite an existing file**

After saving, display the filename and a brief summary:
```
Saved: reviewer-response-v1.md
Reviewers: 2 | Total comments: 8 (3 Major, 5 Minor)
Placeholders requiring author input: 4
Proposed citations: 2 (need author approval)
```

---

## Data policy reminder

Before starting, note internally:
Reviewer comments and manuscript content are confidential.
Confirm with the user that using Claude for this manuscript is approved
under Meteor Biotech's data policy before proceeding with sensitive unpublished data.
