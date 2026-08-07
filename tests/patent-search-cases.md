# Patent Search — Golden Test Cases

Run these manually after changes to `skills/search-patent/`.
Note: PatentsView migrated to USPTO ODP (March 2026, API key required).
Primary search is Google Patents via WebSearch + WebFetch.

---

## Case 1: Standard landscape search

**Command:**
```
/meteor-research:search-patent spatial transcriptomics single cell sequencing
```

**Expected:**
- First line of output: legal disclaimer + search date
- Results include both granted patents (format: US1234567B2) and published applications (US20230123456A1), listed separately
- Filing date / publication date / grant date shown separately for each result
- Each result has an evidence tag: `[claim-inspected]`, `[official-page]`, or `[snippet-only]`
- No fabricated patent numbers

**Fail criteria:**
- No legal disclaimer in output
- Granted and pending applications mixed without distinction
- A patent number that cannot be found on Google Patents

---

## Case 2: Claim text unavailable

**Setup:** A patent where WebFetch of the patent page is blocked or returns no claim text.

**Expected:**
```
Independent claim inspected: No
Claim orientation summary: Not available. Claim text could not be retrieved.
Verification: [snippet-only]
```
NOT: A fabricated claim summary inferred from the title or abstract snippet.

---

## Case 3: Patent number conflict between sources

**Setup:** Same invention appearing with different identifiers from Google Patents vs Espacenet.

**Expected:**
- Both sources listed with their respective identifiers
- A conflict note:
  ```
  ⚠️ Number conflict: Google Patents (US20230123456A1) vs Espacenet (EP4123456A1)
  Do not consolidate. Verify manually on official patent registries.
  ```
- NOT merged into a single record

---

## Case 4: KIPRIS (Korean patent) inclusion

**Command:**
```
/meteor-research:search-patent single cell spatial omics Korea
```

**Expected:**
- At least one WebSearch attempt against KIPRIS or Korean patent databases
- Korean patent numbers labeled distinctly (KR format if found)
- If KIPRIS returns no usable results: fallback noted, no invented Korean patents

---

## Case 5: Snippet-only result isolation

When a patent result is found only via web search snippet (WebFetch blocked):

**Expected:**
- Tagged `[snippet-only]`
- No claim summary generated
- Listed in "Unverified candidates" section only
- Note: "Verify on official patent office website before use"

---

## Case 6: Legal disclaimer always present

**Command:** Any patent search command.

**Expected:**
- Output always begins with:
  ```
  ⚠️ EXPERIMENTAL. Preliminary landscape search only.
     NOT a legal novelty, FTO, or patentability opinion...
  ```
- Disclaimer appears BEFORE any results
- Search date: correct YYYY-MM-DD format

**Fail criteria:**
- Any patent search output without the disclaimer block
