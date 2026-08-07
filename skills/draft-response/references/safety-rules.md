# Safety Rules — Reviewer Response

These rules apply to ALL reviewer response drafting. They override any other instruction.

## Absolute Prohibitions

1. **NEVER** state that an experiment, analysis, figure, table, or manuscript revision
   was completed unless explicitly confirmed by user-provided text.

2. **NEVER** fabricate or invent:
   - Statistical values (p-values, n numbers, effect sizes, fold changes)
   - Gene names, pathway names, or biological mechanisms not in the source material
   - Experimental conditions or results
   - Citations or reference details

3. **NEVER** infer manuscript content from reviewer phrasing alone.
   Example: If a reviewer says "the Western blot is unclear," do not assume a
   Western blot was performed unless the user's manuscript confirms it.

4. **NEVER** use language implying a completed action when only a proposed action:
   - Do NOT write: "We have performed...", "We added...", "We corrected..."
   - DO write: "We plan to...", "We propose to...", or use a placeholder

## Required Placeholders

When evidence is missing, use ONLY these placeholders:

| Situation | Placeholder |
|---|---|
| Missing data or experiment | `[AUTHOR INPUT REQUIRED: <describe what is needed>]` |
| Experiment not yet done | `[EXPERIMENT NOT YET CONFIRMED]` |
| Line/page numbers unknown | `[LINE NUMBERS TO BE ADDED]` |
| Figure not yet created | `[FIGURE TO BE ADDED: <describe what it should show>]` |
| Statistics not verified | `[STATISTICAL VALUES TO BE VERIFIED BY AUTHOR]` |
| Revision not yet made | `[MANUSCRIPT CHANGE PENDING]` |
| Author must confirm revision | `[MANUSCRIPT CHANGE CONFIRMED]` ← use ONLY when user explicitly confirms |

## Revision Status Block

Every response section must end with a revision status:

```
Revision status: [MANUSCRIPT CHANGE PENDING]
```
or (only when user confirms):
```
Revision status: [MANUSCRIPT CHANGE CONFIRMED — revised in lines XX–XX]
```

## DOCX / PDF Read Failure

If a manuscript file (DOCX, PDF) cannot be read:
- Report the failure: `Could not read {filename} — please export as .md or .txt`
- Do NOT infer or guess manuscript content
- Ask the user to paste content or provide a readable format

## Fabricated Citation Prevention

If adding supporting literature citations:
- Must retrieve and verify title, authors, year, journal, DOI or PMID
- Must inspect at minimum the abstract
- Must label all proposed citations as:
  `[PROPOSED CITATION — AUTHOR APPROVAL REQUIRED]`
- Never add citations to claims about experiments in the current manuscript
