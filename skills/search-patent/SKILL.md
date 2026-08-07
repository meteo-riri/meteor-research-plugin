---
name: search-patent
description: >
  Technology landscape search across patent databases (USPTO, Google Patents,
  Espacenet, KIPRIS). Triggers: "특허 검색", "선행기술 조사", "prior art search",
  "search patents", "특허 있어?", "patent landscape for X".
  NOT legal advice. Results require attorney review before any business decision.
argument-hint: "<technology, biomarker, method, compound, or concept>"
allowed-tools: Read, WebSearch, WebFetch, Write
---

# search-patent

⚠️ **EXPERIMENTAL — v0.3**

## Step 0 — Load mandatory policy

Read `${CLAUDE_PLUGIN_ROOT}/references/anti-fabrication.md`.
Read `<skill-dir>/references/output-schema.md` for field definitions and claim rules.

If either file cannot be loaded, STOP and report:
> "Mandatory policy files could not be loaded. Patent search aborted."

**Print this disclaimer at the very top of ALL output, before any results:**
```
⚠️ EXPERIMENTAL. Preliminary landscape search only.
   NOT a legal novelty, FTO, or patentability opinion.
   Patent status, ownership, and claims change frequently.
   Consult a registered patent attorney before any business decision.
   Search date: {YYYY-MM-DD}
```

---

## Step 1 — Determine query

Use `$ARGUMENTS` as the search query. If empty, ask:
> "What technology, method, or concept would you like to search patents for?"

Expand biomedical terms (3–4 synonyms max):
- Gene names → include protein name and common aliases
- Method names → include synonyms (e.g., "spatial transcriptomics" → also "in situ sequencing")

---

## Step 2 — Search databases

### 2a. Google Patents (primary)

**Step 1 — WebSearch to find candidates:**
```
WebSearch: site:patents.google.com {QUERY}
```
Collect up to 10 patent/application URLs from search snippets.

From each snippet, extract what is visible:
- Patent/application number (e.g., US20190276881A1, WO2020123742A1)
- Title
- Assignee (if shown)
- Date (if shown)
- Brief description from snippet

**Step 2 — WebFetch each patent page:**
For each URL found, attempt:
```
WebFetch: https://patents.google.com/patent/{number}/en
```
If WebFetch succeeds, extract:
- Full title, assignee, inventors
- Filing date, publication date, grant date (distinguish these three)
- Abstract
- Claim 1 text (first independent claim)
→ Tag as `[official-page]` (abstract) or `[claim-inspected]` (if claim text retrieved)

If WebFetch fails or is blocked:
→ Use snippet data only, tag as `[snippet-only]`
→ Do NOT infer claim text or abstract from title alone

### 2b. Espacenet (European/international)

```
WebSearch: site:worldwide.espacenet.com {QUERY}
```
Collect patent numbers and titles from snippets.
Attempt WebFetch of individual pages if URLs are found.
Tag results appropriately: `[official-page]` or `[snippet-only]`.

### 2c. KIPRIS (Korean patents — run only if user requests or query is biotech/pharma)

```
WebSearch: site:patent.kipris.or.kr {QUERY}
```
Typically returns `[snippet-only]` results. Note language: KIPRIS pages are in Korean.

### 2d. USPTO Open Data Portal (optional — requires API key)

The USPTO PatentsView API migrated to data.uspto.gov in March 2026 and now requires
an API key. If the user has configured a USPTO ODP API key, use:
```
GET https://data.uspto.gov/api/v1/patent/search
  ?q={QUERY}&dateRange=grantDate:[2015-01-01 TO *]&hits.hits.total.value=true
  Authorization: Bearer {USPTO_ODP_API_KEY}
```
If no API key is available, skip this source and note: `USPTO ODP not searched (API key required)`.

---

## Step 3 — Deduplicate

1. Exact patent number match → deduplicate
2. Same invention, different jurisdiction (US + EP + WO family) → group as patent family, show all numbers
3. If two sources give conflicting numbers for the same apparent patent → flag conflict, show both

---

## Step 4 — Classify and output

Use output format from `<skill-dir>/references/output-schema.md`.

Group results:
- **Granted patents** — number format ends in B1, B2, etc.
- **Published applications** — number format ends in A1, A2, etc.
- **[snippet-only] candidates** — unverified, snippet data only

---

## Step 5 — Save (on user request only)

File naming: `patent-search-{sanitized-query}-{YYYY-MM-DD}.md`
If exists: `-2`, `-3` suffix. Never overwrite.

Always append:
```
Search performed: {YYYY-MM-DD}
Sources searched: Google Patents, Espacenet, KIPRIS (if searched)
USPTO ODP: {searched with key / not searched — API key required}
DISCLAIMER: Not a legal opinion. Verify all results with official patent registers.
```
