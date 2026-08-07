---
name: search-paper
description: >
  Search external biomedical literature when the user explicitly asks to find,
  identify, retrieve, or search for papers or recent studies on a specific topic.
  Triggers: "find papers about X", "search the literature for X", "논문 찾아줘",
  "관련 연구 검색해줘", "최신 논문 알려줘", "identify prior work on X",
  "what papers exist on X", "X에 대한 논문 있어?".
  Do NOT invoke when the user is merely discussing, summarizing, or asking questions
  about a paper already provided in the conversation or current directory.
argument-hint: "<topic, method, disease, or research question>"
allowed-tools: Read, WebFetch, WebSearch, Write, Agent
---

# search-paper

## Step 0 — Load mandatory policy

Read `${CLAUDE_PLUGIN_ROOT}/references/anti-fabrication.md`.

If the file cannot be found, try `../../references/anti-fabrication.md` relative to this skill directory.

If the file still cannot be loaded, STOP and report:
> "Mandatory plugin policy could not be loaded. Search aborted."

Do NOT proceed until the policy is confirmed loaded.

---

## Step 1 — Determine query

If invoked via slash command with arguments, use `$ARGUMENTS` as the search query.
Do NOT ask the user to repeat a query already in `$ARGUMENTS`.

If invoked via natural language (no `$ARGUMENTS`), extract the research topic from the conversation.
If ambiguous, ask once: "What topic or question would you like me to search for?"

Load `<skill-dir>/references/query-strategy.md` and apply expansion rules to the query.

---

## Step 2 — Search databases in priority order

Execute sources in this order. Stop adding sources once you have ≥15 candidate papers.

### 2a. PubMed (mandatory first)

1. **ESearch** — retrieve up to 20 PMIDs:
   ```
   https://eutils.ncbi.nlm.nih.gov/entrez/eutils/esearch.fcgi
     ?db=pubmed&term={ENCODED_QUERY}&retmax=20&retmode=json&sort=pub_date
   ```

2. **ESummary** — retrieve title/author/journal/date for ALL PMIDs in ONE batch call:
   ```
   https://eutils.ncbi.nlm.nih.gov/entrez/eutils/esummary.fcgi
     ?db=pubmed&id={PMID1,PMID2,...}&retmode=json
   ```

3. **EFetch** — retrieve abstracts for ALL PMIDs in ONE batch call:
   ```
   https://eutils.ncbi.nlm.nih.gov/entrez/eutils/efetch.fcgi
     ?db=pubmed&id={PMID1,PMID2,...}&retmode=xml&rettype=abstract
   ```

**NEVER call EFetch or ESummary once per PMID. Always batch.**

If PubMed is unavailable, log: `PubMed API unavailable — proceeding to Europe PMC.`

### 2b. Europe PMC

```
https://www.ebi.ac.uk/europepmc/webservices/rest/search
  ?query={ENCODED_QUERY}&format=json&resulttype=core&pageSize=20&sort=P_PDATE_D%20desc
```

Extract: `pmid`, `pmcid`, `doi`, `title`, `authorString`, `journalTitle`, `pubYear`, `abstractText`

### 2c. bioRxiv (preprints)

```
https://api.biorxiv.org/details/biorxiv/{START_DATE}/{END_DATE}/0
```
where `START_DATE` = today minus 30 days in `YYYY-MM-DD` format, `END_DATE` = today in `YYYY-MM-DD` format.
Example: `https://api.biorxiv.org/details/biorxiv/2026-07-08/2026-08-07/0`

**IMPORTANT:** The `Nd` shorthand (e.g., `30d`) is NOT supported — always compute and pass explicit dates.

Filter results locally: keep only papers where `title` OR `abstract` contains query keywords.
Handle pagination: check `messages[0].count` for total; if total > 30, fetch cursor=30, 60, etc. until relevance drops or 90 results reached.
Do the same for medRxiv: replace `biorxiv` with `medrxiv` in the URL.

### 2d. Publisher fallback (conditional)

Run ONLY if PubMed + Europe PMC results < 5 OR user explicitly asks:
```
WebSearch: site:nature.com {QUERY}
WebSearch: site:cell.com {QUERY}
WebSearch: site:science.org {QUERY}
```
For each result, attempt WebFetch of the article page to get DOI and abstract.
Verify any DOI found via Crossref: `https://api.crossref.org/works/{DOI}`

### 2e. General WebSearch (last resort)

Only if all above sources return < 5 results total. Results go to "Unverified candidates" only.

---

## Step 3 — Deduplicate and classify

Apply deduplication rules from `<skill-dir>/references/output-schema.md`.

Classify each entry:
- **Verified**: confirmed in PubMed, Europe PMC, bioRxiv, or Crossref → main table
- **Provisional**: found on publisher page, DOI not yet confirmed → provisional section
- **Unverified**: only found via web snippet → unverified section, `[snippet-only]` tag

---

## Step 4 — Generate output

Load `<skill-dir>/references/output-schema.md` for field definitions.

For each verified result, output:
```
### [N]. {Title}
- Authors: {authors}
- Journal/Server: {journal or server}
- Date: {YYYY-MM-DD}
- DOI: {doi or "Not available"}
- PMID: {pmid or "Not available"}
- Type: {type} {[peer-reviewed] or [preprint]}
- Source: {DB name} — {API endpoint URL}
- Evidence: {[title-only] | [abstract] | [full-text] | [partial-text]}

**Summary:**
1. {Research objective and study design}
2. {Key finding}
3. {Relevance to query or notable limitation}

Basis: direct retrieval
```

**Summary generation rule:** Only generate a summary if `[abstract]` or higher evidence. Otherwise:
`Summary: Not available (abstract not retrieved).`

Output up to 10 entries in the main table.
Add provisional and unverified sections below if applicable.

---

## Step 5 — Deep analysis (optional)

If the user asks for detailed analysis of a specific paper from the results, delegate to:
`meteor-research:literature-reviewer`

Pass the paper's DOI or PMID, title, and retrieved text.

---

## Step 6 — Save (conditional)

Save a Markdown file ONLY when the user explicitly requests saving/exporting.

Default behavior: display results only. Do NOT create files automatically.

When saving:
- Path: `papers/search-{sanitized-query}-{YYYY-MM-DD}.md`
- If file exists: use `-2`, `-3` suffix. Never overwrite.

---

## Data policy reminder

Before starting, note internally:
Do not include confidential project names, unpublished targets, patient identifiers,
or patent-sensitive concepts in any search query sent to external APIs.
