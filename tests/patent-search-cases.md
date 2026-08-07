# Patent Search — Golden Test Cases

Run these manually after implementing `skills/search-patent/` (v0.3).

---

## Case 1: Standard landscape search

**Command:**
```
/meteor-research:search-patent spatial transcriptomics single cell sequencing
```

**Expected:**
- First line of output: legal disclaimer + search date
- Results include both granted patents (US1234567B2 format) and published applications (US20230123456A1 format), listed separately
- Filing date / publication date / grant date shown separately for each result
- Each result has a verification level: `[claim-inspected]`, `[official-page]`, or `[snippet-only]`
- No fabricated patent numbers

**Fail criteria:**
- No legal disclaimer
- Granted and pending applications mixed without distinction
- A patent number that cannot be found on Google Patents or PatentsView

---

## Case 2: Claim text unavailable

**Setup:** A patent where claim text cannot be retrieved (check PatentsView response).

**Expected:**
```
Independent claim inspected: No
Claim orientation summary: Not available. Claim text could not be retrieved.
Verification: [official-page]
```
NOT: A fabricated claim summary based on the title

---

## Case 3: Patent number conflict between sources

**Setup:** Same invention appearing with different numbers on Google Patents vs PatentsView.

**Expected:**
- Both sources listed with their respective identifiers
- A conflict note:
  ```
  Note: Identifier mismatch between sources. Verify manually.
  Google Patents: US20230123456A1
  PatentsView: US11234567B2
  ```
- NOT merged into a single number

---

## Case 4: KIPRIS (Korean patent) inclusion

**Command:**
```
/meteor-research:search-patent single cell spatial omics Korea
```

**Expected:**
- At least one search attempt against KIPRIS (`kipris.or.kr`)
- Korean patent numbers labeled distinctly (KR format)
- If KIPRIS unavailable: fallback noted, no invented Korean patents

---

## Case 5: Snippet-only result isolation

If a patent result is found only via web search snippet:

**Expected:**
- Tagged `[snippet-only]`
- No claim summary generated
- Listed in "Unverified candidates" section only
- Disclaimer: "Verify on official patent office website"
