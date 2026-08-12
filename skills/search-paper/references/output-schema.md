# Output Schema — Paper Search Results

## Required Fields (every entry)

```
IF:             [Journal Impact Factor — 2-year JCR, most recent year available,
                 e.g. "42.5 (2024 JCR)". Source: WebSearch "{journal}" impact factor JCR.
                 Preprint servers: "N/A (preprint)". Not found: "IF: Not available".]
Citations:      [Crossref referenced-by-count via https://api.crossref.org/works/{DOI}.
                 Note: Crossref counts may be lower than Google Scholar.
                 Format: "N (Crossref, YYYY-MM-DD)". No DOI: "Not available".]
Title:          [exact title from source, DO NOT modify]
Authors:        [as returned by API; "et al." acceptable for >6 authors]
Journal/Server: [journal name or "bioRxiv" / "medRxiv"]
Date:           [YYYY-MM-DD or YYYY-MM if day unavailable]
DOI:            [exact DOI string, or "Not available"]
PMID:           [PubMed ID, or "Not available"]
PMCID:          [PubMed Central ID, or "Not available"]
bioRxiv ID:     [version ID if preprint, or "Not applicable"]
Type:           [Original Article | Review | Letter | Preprint | Other]
Status:         [[peer-reviewed] | [preprint]]
Source DB:      [PubMed | Europe PMC | bioRxiv | Crossref | Publisher | WebSearch]
API endpoint:   [exact URL used to retrieve this record]
Evidence:       [[title-only] | [abstract] | [full-text] | [partial-text] | [snippet-only]]
```

## Fetching IF and Citation Count

**Citation count** — for every paper with a DOI, call Crossref in batch where possible:
```
https://api.crossref.org/works/{DOI}  →  message.referenced-by-count
```
Batch via `https://api.crossref.org/works?filter=doi:{DOI1},{DOI2}` if available.
Always note the retrieval date next to the count.

**Impact Factor** — look up once per unique journal via WebSearch:
```
"{journal name}" impact factor JCR 2024
```
Accept the first clearly sourced JCR or Clarivate value. Note the data year.
Cache within the session — do not repeat the same journal lookup twice.
If conflicting values appear, use the lower figure and note "varies by source".

## Summary (required when evidence = [abstract] or higher)

Three sentences, each with a fixed role:
1. **Objective & design**: What the study investigated and how (study type, model, method)
2. **Key finding**: The main result or conclusion
3. **Relevance**: Why this is relevant to the user's query, OR a notable limitation

**Do NOT generate a summary when evidence = `[title-only]` or `[snippet-only]`.**
Instead write: `Summary: Not available (abstract not retrieved).`

## Inference Flag

After the summary, add one line:
- `Basis: direct retrieval` — when summary is drawn from retrieved abstract/text
- `Basis: assistant inference` — if ANY part of the summary is inferred (avoid this)

## Deduplication Rules

Before outputting, remove duplicates using this priority:
1. Exact DOI match → keep the entry with the highest evidence level
2. PMID / PMCID match
3. bioRxiv preprint linked to a published paper → keep both, link them:
   ```
   Published version: DOI 10.xxxx/xxx  [peer-reviewed]
   Related preprint:  bioRxiv 2025.01.01.123456  [preprint]
   ```
4. Normalized title + first author + year → merge if all match

## Ranking Priority

Rank results in this order (highest priority first):
1. Direct relevance to the user's full query terms
2. Evidence level quality (full-text > abstract > title-only)
3. Study type match (if user specified Original Article / Review / etc.)
4. Recency (within relevance tier, newer is higher)
5. Journal prominence — secondary signal ONLY, do not promote over more relevant papers

## Output Sections

### Main table: Verified results (up to 10)
Entries with metadata confirmed by PubMed, Europe PMC, bioRxiv, or Crossref.

### Provisional results (optional)
Entries found on publisher pages but not yet confirmed in primary DBs.
Note: `Provisional — verify manually in PubMed or Europe PMC.`

### Unverified candidates (optional)
`[snippet-only]` entries from WebSearch. No summary, no DOI generation.
Note: `Unverified — found via web search snippet only. Confirm before citing.`
