---
name: search-patent
description: >
  Technology landscape search across patent databases (USPTO, Google Patents,
  Espacenet, KIPRIS). Triggers: "특허 검색", "선행기술 조사", "prior art search",
  "search patents", "특허 있어?", "patent landscape for X".
  NOT legal advice. Results require attorney review before any business decision.
argument-hint: "<technology, biomarker, method, compound, or concept>"
allowed-tools: Read, WebFetch, WebSearch, Write
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

Expand biomedical terms:
- Gene names → include protein name, common aliases
- Method names → include synonyms (e.g., "spatial transcriptomics" → "in situ sequencing")
- Do NOT over-expand — 3–4 terms max

---

## Step 2 — Search databases in priority order

### 2a. PatentsView (USPTO granted patents)

```
POST https://search.patentsview.org/api/v1/patent/
Content-Type: application/json

{
  "q": {"_text_any": {"patent_title": "{QUERY}", "patent_abstract": "{QUERY}"}},
  "f": ["patent_number", "patent_title", "patent_date", "patent_abstract",
        "assignee_organization", "inventor_last_name", "app_number", "app_date"],
  "o": {"per_page": 10, "sort": [{"patent_date": "desc"}]}
}
```

For each result, attempt to retrieve independent claims:
```
GET https://search.patentsview.org/api/v1/patent/{patent_number}?f=["claims"]
```
Tag as `[claim-inspected]` if claims retrieved, `[official-page]` if abstract only.

### 2b. PatentsView (USPTO published applications)

```
POST https://search.patentsview.org/api/v1/publication/
Content-Type: application/json

{
  "q": {"_text_any": {"app_title": "{QUERY}", "app_abstract": "{QUERY}"}},
  "f": ["app_number", "app_title", "app_date", "app_abstract",
        "assignee_organization", "inventor_last_name"],
  "o": {"per_page": 10, "sort": [{"app_date": "desc"}]}
}
```

### 2c. Google Patents (WebSearch)

```
WebSearch: site:patents.google.com {QUERY}
```

For each hit, attempt WebFetch of the patent page to retrieve:
- Patent/application number
- Title, abstract, assignee, filing date, grant date
- Claim 1 text (independent claim)

Tag as `[official-page]` if page fetched successfully, `[snippet-only]` if not.

### 2d. Espacenet (European patents, if relevant)

```
WebFetch: https://worldwide.espacenet.com/patent/search?q={URL_ENCODED_QUERY}
```

Extract results if accessible. Tag as `[official-page]` or `[snippet-only]`.

### 2e. KIPRIS (Korean patents, if user requests or query is Korea-relevant)

```
WebSearch: site:patent.kipris.or.kr {QUERY}
```

Tag all KIPRIS results as `[snippet-only]` unless full page is accessible.

---

## Step 3 — Deduplicate

Remove duplicates by:
1. Exact patent number match (US1234567B2 == US1234567B2)
2. Same application number → keep granted patent, note application
3. Family members (same invention, different jurisdiction) → group together, do NOT merge into one entry

---

## Step 4 — Classify and output

Use output format from `<skill-dir>/references/output-schema.md`.

Group results into:
- **Granted patents** — issued, currently in force (unverified — confirm with official register)
- **Published applications** — pending, not yet granted
- **[snippet-only] candidates** — search snippet only, not verified

---

## Step 5 — Save (on user request only)

File naming: `patent-search-{sanitized-query}-{YYYY-MM-DD}.md`
If exists: `-2`, `-3` suffix. Never overwrite.

Always append to saved file:
```
Search performed: {YYYY-MM-DD}
Sources: PatentsView, Google Patents, Espacenet, KIPRIS (if searched)
DISCLAIMER: Not a legal opinion. Verify all results with official patent registers.
```
