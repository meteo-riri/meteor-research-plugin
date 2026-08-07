---
name: monitor-spatial
description: >
  Check for new spatial omics papers published since the last monitoring run.
  Triggers ONLY on explicit user request: "spatial omics 모니터링", "새 spatial 논문",
  "최신 spatial 논문 확인", "monitor spatial", "check new spatial papers".
  Do NOT auto-trigger from general paper discussion.
disable-model-invocation: true
allowed-tools: Read, WebFetch, Write
---

# monitor-spatial

## Step 0 — Load mandatory policy

Read `${CLAUDE_PLUGIN_ROOT}/references/anti-fabrication.md`.
If it cannot be loaded, STOP and report:
> "Mandatory policy could not be loaded. Monitoring aborted."

Read `<skill-dir>/references/monitoring-rules.md` for state file format and paths.
Read `<skill-dir>/references/spatial-keywords.md` for keyword list.

---

## Step 1 — Load state

State file path: `.meteor-research/spatial-monitor-state.json` (current working directory)

If the file exists and is valid JSON, read:
- `last_run`: date of previous run (YYYY-MM-DD)
- `seen_dois`: array of DOIs already reported
- `seen_pmids`: array of PMIDs already reported
- `seen_biorxiv_ids`: array of bioRxiv IDs already reported

If the file is missing: treat as first run — `last_run = null`, all arrays empty.

If the file exists but is malformed JSON:
1. Write a backup: `.meteor-research/spatial-monitor-state.bak`
2. Reset to empty state
3. Report: `State file was malformed — reset to empty. Backup saved.`
Do NOT silently delete.

---

## Step 2 — Determine search window

**Manual local run:**
- If `last_run` is set: search from `last_run` to today
- If `last_run` is null (first run): search past 14 days

**Cloud Routine detection:**
If running in a Routine (fresh clone, no pre-existing state file):
- Always search past 7 days regardless of state
- Do NOT attempt to persist state (write is allowed but will not survive next run)
- Add note to output: `Routine run — stateless digest, past 7 days`

Use today's date in YYYY-MM-DD format throughout.

---

## Step 3 — Search

Load keywords from `<skill-dir>/references/spatial-keywords.md`.

### 3a. PubMed

Build query from primary keywords (see spatial-keywords.md — use Group A terms):
```
https://eutils.ncbi.nlm.nih.gov/entrez/eutils/esearch.fcgi
  ?db=pubmed
  &term={KEYWORD_QUERY}
  &datetype=pdat
  &mindate={last_run or today-14d}
  &maxdate={today}
  &retmax=50
  &retmode=json
  &sort=pub_date
```

Batch ESummary for all PMIDs, then EFetch for abstracts of non-seen results.

### 3b. bioRxiv

```
https://api.biorxiv.org/details/biorxiv/{START_DATE}/{END_DATE}/0
```
Filter locally using keywords from spatial-keywords.md (title + abstract).
Paginate if total > 30.

Also check medRxiv with same date window.

### 3c. Deduplication against state

Skip any paper where:
- DOI is in `seen_dois`
- PMID is in `seen_pmids`
- bioRxiv ID is in `seen_biorxiv_ids`

---

## Step 4 — Format output

Group new papers by topic using spatial-keywords.md Group labels.

For each paper:
```
**{Title}** [{evidence level}]
{First Author} et al. — {Journal/Server} ({Year})
DOI: {DOI or "Not available"} | PMID: {PMID or "—"}
{2-sentence summary: objective + key finding}
```

Output header:
```
## Spatial Omics Monitor — {today}
Window: {start_date} → {today}
New papers: {N} | Sources: PubMed, bioRxiv, medRxiv
```

If no new papers:
> No new spatial omics papers found since {last_run}.

---

## Step 5 — Update state and save report

**Update state file:**
```json
{
  "last_run": "{today}",
  "seen_dois": [...existing + new],
  "seen_pmids": [...existing + new],
  "seen_biorxiv_ids": [...existing + new]
}
```
Write to `.meteor-research/spatial-monitor-state.json`.
Create the `.meteor-research/` directory if it does not exist.

**Save report** (always):
```
.meteor-research/reports/spatial-monitor-{today}.md
```

**Cloud Routine note** (append to output if detected):
```
Routine run — stateless digest, past 7 days.
State is NOT persisted across Routine runs.
Storage path: reports/spatial-monitor/ (repo-relative).
Requires Pro/Max/Team/Enterprise with Routines enabled.
```
