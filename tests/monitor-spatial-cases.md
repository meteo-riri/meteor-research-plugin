# Spatial Monitor — Golden Test Cases

Run these manually after changes to `skills/monitor-spatial/`.

---

## Case 1: First run (no state file)

**Setup:** Ensure `.meteor-research/spatial-monitor-state.json` does not exist.

**Command:** `/meteor-research:monitor-spatial`

**Expected:**
- Searches past 14 days (first-run default)
- Creates `.meteor-research/spatial-monitor-state.json` after run
- State file contains today's date in `last_run`, plus `seen_dois`, `seen_pmids`, `seen_biorxiv_ids`
- Report saved to `.meteor-research/reports/spatial-monitor-YYYY-MM-DD.md`
- Output header shows: `Window: {14 days ago} → {today}`

**Fail criteria:**
- State file not created
- Report not saved
- Search window other than 14 days

---

## Case 2: Incremental run (state file exists)

**Setup:** Run Case 1 first. Then immediately run again.

**Command:** `/meteor-research:monitor-spatial` (second run)

**Expected:**
- Output: `No new spatial omics papers found since {yesterday's date}`
  (or small number of new papers if published in the past few minutes)
- Previously seen papers do NOT reappear
- `last_run` in state file updated to today
- New report file created (not overwriting previous report)

**Fail criteria:**
- Same papers listed again from first run
- State file not updated

---

## Case 3: Malformed state file recovery

**Setup:** Write invalid JSON to `.meteor-research/spatial-monitor-state.json`:
```bash
echo "{ broken json" > .meteor-research/spatial-monitor-state.json
```

**Command:** `/meteor-research:monitor-spatial`

**Expected:**
- Backup created: `.meteor-research/spatial-monitor-state.bak`
- State reset to empty, first-run behavior (14-day window)
- User notified: `State file was malformed — reset to empty. Backup saved.`
- Run completes normally

**Fail criteria:**
- State file silently deleted (no backup)
- Skill aborts without explanation
- Malformed state causes error with no recovery

---

## Case 4: Results grouped by topic

**Expected output format:**
```
## Spatial Omics Monitor — YYYY-MM-DD
Window: YYYY-MM-DD → YYYY-MM-DD
New papers: N | Sources: PubMed, bioRxiv, medRxiv

### Tumor microenvironment
**Paper Title** [abstract]
Author et al. — Journal (Year)
DOI: ... | PMID: ...
2-sentence summary.

### Methods & Tools
...
```

**Fail criteria:**
- No topic grouping — all papers in flat list
- Papers without evidence level tags

---

## Case 5: Keyword relevance filtering

**Expected:** Only papers where title or abstract contains Group A or Group B keywords
from `spatial-keywords.md` appear in output.

**Verify:** Pick one result and confirm it contains a relevant keyword.
Pick one excluded paper and confirm it does NOT contain relevant keywords.

**Fail criteria:**
- General biology papers unrelated to spatial omics appearing in results
