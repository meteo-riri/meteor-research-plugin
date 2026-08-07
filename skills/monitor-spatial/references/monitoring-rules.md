# Monitoring Rules — State and Deduplication

## State File

**Path:** `.meteor-research/spatial-monitor-state.json` (current working directory)

**Format:**
```json
{
  "last_run": "YYYY-MM-DD",
  "seen_dois": ["10.xxxx/yyyy", ...],
  "seen_pmids": ["12345678", ...],
  "seen_biorxiv_ids": ["2026.01.01.123456", ...]
}
```

**Rules:**
- Create `.meteor-research/` directory if missing
- Write state AFTER reporting results, not before
- If state write fails: report warning, do not abort
- Never truncate `seen_*` arrays — append only

## Deduplication Priority

1. DOI match (exact string, case-insensitive) → skip
2. PMID match → skip
3. bioRxiv ID match (from `doi` field, e.g. `10.1101/2026.xx.xx`) → skip
4. If a preprint DOI and a published DOI point to the same paper (`rel_doi` field in bioRxiv): add BOTH to seen lists, report as peer-reviewed version only

## Malformed State Recovery

1. Copy existing file to `.meteor-research/spatial-monitor-state.bak`
2. Reset state to empty (`last_run: null`, empty arrays)
3. Report to user: `State file was malformed — reset to empty. Backup at .meteor-research/spatial-monitor-state.bak`
4. Continue with first-run behavior (past 14 days)

## Search Window Rules

| Situation | Window |
|---|---|
| First run (no state) | Past 14 days |
| Normal run (state exists) | `last_run` → today |
| Gap > 60 days | Cap at 60 days, warn user |
| Cloud Routine (stateless) | Past 7 days, always |

## Report File

**Path:** `.meteor-research/reports/spatial-monitor-{YYYY-MM-DD}.md`

If a report for today already exists (re-run same day): append `-2`, `-3` suffix.
Never overwrite an existing report.
