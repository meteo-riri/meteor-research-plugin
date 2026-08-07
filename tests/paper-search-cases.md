# Paper Search — Golden Test Cases

Run these manually after each change to `skills/search-paper/`.
For each case: run the command, then verify the expected criteria by hand.

---

## Case 1: Standard query — known active research area

**Command:**
```
/meteor-research:search-paper MERFISH spatial transcriptomics breast cancer
```

**Expected:**
- At least 3 results from PubMed or Europe PMC with real PMIDs
- Every DOI in the results matches a real paper when looked up at `https://doi.org/{DOI}`
- Preprints are labeled `[preprint]`; peer-reviewed articles labeled `[peer-reviewed]`
- Each result has an evidence level tag: `[abstract]` or `[full-text]` (not `[snippet-only]`)
- Three-sentence summary present for every abstract-level result
- No result appears more than once (DOI or PMID deduplication)
- Maximum 10 results in the main "Verified results" table
- Source DB + API endpoint listed for each result

**Fail criteria (any = test failure):**
- A DOI that returns 404 at doi.org
- A PMID that does not exist in PubMed
- A summary generated without an abstract being retrieved
- More than 10 results in the main table
- A result in the main table tagged `[snippet-only]`

---

## Case 2: Overt false-positive risk — ambiguous term

**Command:**
```
/meteor-research:search-paper Visium
```

**Expected:**
- Results centered on 10x Genomics Visium spatial transcriptomics platform
- Zero or minimal results about unrelated Latin/English uses of "visium"
- If ambiguous results appear, they are in "Provisional" or "Unverified" section only

---

## Case 3: Zero-results query — nonexistent topic

**Command:**
```
/meteor-research:search-paper XYZQ_FAKE_TARGET_99999 spatial omics
```

**Expected:**
- Output: `No results found for: XYZQ_FAKE_TARGET_99999 spatial omics`
- 2–3 alternative query suggestions
- **Zero papers generated** — no plausible-sounding titles, authors, or DOIs
- No three-sentence summaries

**Fail criteria:**
- Any paper title, author, DOI, or PMID appears in the output

---

## Case 4: PubMed unavailable (simulate by checking fallback behavior)

**Setup:** Temporarily note whether PubMed returns an error (e.g., rate-limit).

**Expected:**
- Explicit error message: `PubMed API unavailable — falling back to Europe PMC`
- Results from Europe PMC if available
- No invented papers to fill the gap
- Search source listed as `Europe PMC` (not `PubMed`)

---

## Case 5: Very recent preprint (bioRxiv)

**Command:**
```
/meteor-research:search-paper spatial transcriptomics single cell 2026
```

**Expected:**
- At least one bioRxiv result with a valid bioRxiv ID (format: `YYYY.MM.DD.NNNNNN`)
- bioRxiv results labeled `[preprint]`
- If same work appears as both preprint and peer-reviewed, they are linked not duplicated:
  ```
  Published version: DOI 10.xxxx/xxx
  Related preprint: bioRxiv 2026.01.01.123456
  ```

---

## Case 6: Save-on-request only

**Command (no save instruction):**
```
/meteor-research:search-paper MERFISH lung cancer
```
**Expected:** No file created in `papers/` directory.

**Command (with save):**
```
/meteor-research:search-paper MERFISH lung cancer — save results
```
**Expected:** File created as `papers/search-MERFISH-lung-cancer-YYYY-MM-DD.md`

**Repeat same command:**
**Expected:** New file created as `papers/search-MERFISH-lung-cancer-YYYY-MM-DD-2.md` (no overwrite)

---

## Case 7: Snippet-only result isolation

If a result can only be retrieved as a web search snippet:

**Expected:**
- Appears only in "Unverified candidates" section
- No DOI generated
- No three-sentence summary
- Not counted in top 10
- Tagged `[snippet-only]`
