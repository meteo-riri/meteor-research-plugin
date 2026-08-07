# Search Databases — API Reference

## Priority Order
1. PubMed E-utilities (primary)
2. Europe PMC (primary)
3. bioRxiv / medRxiv (preprints)
4. Crossref (DOI verification)
5. Publisher sites: nature.com, cell.com, science.org (fallback only)
6. WebSearch / Google Scholar (last resort)

---

## 1. PubMed E-utilities (NCBI)

**Base URL:** `https://eutils.ncbi.nlm.nih.gov/entrez/eutils/`

### Step 1 — ESearch (get PMID list)
```
GET /esearch.fcgi
  ?db=pubmed
  &term={URL_ENCODED_QUERY}
  &retmax=20
  &retmode=json
  &sort=pub_date
  &usehistory=y
```
Returns: JSON with `esearchresult.idlist` (array of PMIDs)

### Step 2 — ESummary (title, authors, journal, date)
```
GET /esummary.fcgi
  ?db=pubmed
  &id={PMID1,PMID2,...}    ← comma-separated batch, NOT individual calls
  &retmode=json
```
Returns: JSON with `result` object keyed by PMID

### Step 3 — EFetch (abstract text)
```
GET /efetch.fcgi
  ?db=pubmed
  &id={PMID1,PMID2,...}    ← same batch
  &retmode=xml
  &rettype=abstract
```
Returns: PubMed XML; extract `<AbstractText>` elements

**Batch rules:**
- ALWAYS combine multiple PMIDs in one request (comma-separated `id=` param)
- NEVER issue one EFetch/ESummary call per PMID
- Maximum ~200 PMIDs per batch call

**Date filtering:**
- Recent papers: add `&datetype=pdat&reldate=30` for past 30 days
- Range: `&datetype=pdat&mindate=2025/01/01&maxdate=2026/12/31`

**Rate limit:** Up to 3 requests/sec without API key. Do NOT loop through PMIDs one-by-one.

---

## 2. Europe PMC

**Base URL:** `https://www.ebi.ac.uk/europepmc/webservices/rest/`

### Search
```
GET /search
  ?query={URL_ENCODED_QUERY}
  &format=json
  &resulttype=core
  &pageSize=20
  &sort=P_PDATE_D%20desc
```
Returns: JSON with `resultList.result[]`

Key fields: `pmid`, `pmcid`, `doi`, `title`, `authorString`, `journalTitle`, `pubYear`, `abstractText`, `isOpenAccess`

**Full text check:** `isOpenAccess: "Y"` → try fetching full text via PMC
```
GET https://www.ebi.ac.uk/europepmc/webservices/rest/{PMCID}/fullTextXML
```

---

## 3. bioRxiv / medRxiv

**Base URL:** `https://api.biorxiv.org/`

### Recent papers (date-based, NOT keyword-based)
```
GET /details/{server}/{START_DATE}/{END_DATE}/{cursor}
  server: biorxiv or medrxiv
  START_DATE: YYYY-MM-DD  (e.g., today minus 30 days)
  END_DATE:   YYYY-MM-DD  (e.g., today)
  cursor: 0, 30, 60... (page through results, 30 per page)
```
Example: `https://api.biorxiv.org/details/biorxiv/2026-07-08/2026-08-07/0`

**IMPORTANT:** The `Nd` shorthand (e.g., `30d`) is NOT supported by the API.
Always compute explicit `YYYY-MM-DD` dates before calling.

**Keyword filtering:** The API returns ALL recent papers. Claude must filter
`title` and `abstract` fields locally for relevance to the query.

**Key fields:** `doi`, `title`, `authors`, `date`, `abstract`, `category`, `version`

**Pagination:** Check `messages[0].count` for total, loop cursor until retrieved >= total or relevance drops.

**Published version link:** If `rel_doi` exists in the record, it links to the peer-reviewed version.

---

## 4. Crossref (DOI Verification)

**Use only to VERIFY a DOI found elsewhere. Never use as primary search source.**

### Verify a known DOI
```
GET https://api.crossref.org/works/{DOI}
```
Returns: full metadata. Compare title, first author, year, journal with what was found.

### Title-based lookup (when DOI is missing)
```
GET https://api.crossref.org/works
  ?query.title={URL_ENCODED_TITLE}
  &rows=5
```
DOI is confirmed ONLY when ALL of these match:
- Normalized title (case-insensitive, punctuation-stripped) ≈ match
- First author surname OR group author matches
- Publication year matches (±0)
- Journal/publisher matches when available

If ANY field conflicts → mark as provisional, do NOT confirm DOI.

---

## 5. Publisher Fallback (nature.com / cell.com / science.org)

**Use ONLY when PubMed + Europe PMC combined < 5 relevant results, OR user explicitly requests publisher-specific search.**

Search strategy:
```
WebSearch: site:nature.com {QUERY}
WebSearch: site:cell.com {QUERY}
WebSearch: site:science.org {QUERY}
```

After finding a result:
- Attempt WebFetch of the article page to retrieve DOI and abstract
- Verify DOI via Crossref (see above)
- If DOI cannot be verified → mark as `[provisional]`

---

## 6. General WebSearch (Last Resort)

Use ONLY as final fallback when all above sources return < 5 results.

Any result found via general WebSearch that cannot be verified in PubMed, Europe PMC, or Crossref MUST be placed in the **"Unverified candidates"** section with tag `[snippet-only]`.
