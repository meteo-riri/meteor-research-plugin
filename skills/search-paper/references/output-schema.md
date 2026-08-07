# Output Schema — Paper Search Results

## Required Fields (every entry)

```
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
