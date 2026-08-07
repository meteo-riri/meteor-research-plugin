# Patent Databases — API Reference

## Priority Order
1. PatentsView — USPTO granted patents (primary, structured API)
2. PatentsView — USPTO published applications
3. Google Patents — broad coverage via WebSearch + WebFetch
4. Espacenet — European and international patents
5. KIPRIS — Korean patents (when relevant)

---

## 1. PatentsView (USPTO)

**Base URL:** `https://search.patentsview.org/api/v1/`

### Granted Patents
```
POST /patent/
{
  "q": {"_text_any": {"patent_title": "{QUERY}", "patent_abstract": "{QUERY}"}},
  "f": [
    "patent_number", "patent_title", "patent_date", "patent_abstract",
    "assignee_organization", "assignee_type",
    "inventor_last_name", "inventor_first_name",
    "app_number", "app_date",
    "uspc_class", "cpc_subgroup_id"
  ],
  "o": {"per_page": 10, "sort": [{"patent_date": "desc"}]}
}
```
Returns: JSON with `patents` array.

### Published Applications
```
POST /publication/
{
  "q": {"_text_any": {"app_title": "{QUERY}", "app_abstract": "{QUERY}"}},
  "f": [
    "app_number", "app_title", "app_date", "app_abstract",
    "assignee_organization", "inventor_last_name"
  ],
  "o": {"per_page": 10, "sort": [{"app_date": "desc"}]}
}
```

### Claims Retrieval
```
GET /patent/{patent_number}?f=["claims"]
```
Returns: `claims` array with `claim_number`, `claim_text`, `independent` flag.

**Claim rules:**
- Retrieve ONLY independent claims for output (independent: true)
- If `independent` flag absent, use Claim 1 as primary independent claim
- Never infer claim scope from title or abstract alone
- If claims endpoint returns 404 or empty: mark `[claim-not-retrieved]`

**Number formats:**
- Granted: `US12345678B2`, `US9876543B1`
- Application: `US20260123456A1`
- NEVER construct or guess patent numbers

---

## 2. Google Patents

**Search via WebSearch:**
```
WebSearch: site:patents.google.com {QUERY} after:2015
```

**Page fetch:**
```
WebFetch: https://patents.google.com/patent/{patent_number}/en
```
Extract: title, abstract, assignee, filing/grant/publication dates, Claim 1 text.

If WebFetch fails or is blocked → mark `[snippet-only]`, do NOT infer claims.

**Number format from Google Patents:**
- US granted: `US12345678B2`
- EP: `EP3456789A1`
- WO (PCT): `WO2026012345A1`

---

## 3. Espacenet (EPO)

**Search:**
```
WebFetch: https://worldwide.espacenet.com/patent/search?q={URL_ENCODED_QUERY}
```

If accessible: extract patent numbers, titles, applicant, dates.
If blocked or returns no structured data: mark all as `[snippet-only]`.

**When to use:** Especially relevant for European competitors or international (PCT) filings.

---

## 4. KIPRIS (Korean Intellectual Property Rights Information Service)

**Search via WebSearch:**
```
WebSearch: site:patent.kipris.or.kr {QUERY}
```

KIPRIS results are almost always `[snippet-only]` from web search.
For Korean-language queries, add Korean synonyms when known.

**When to use:** When user explicitly requests Korean patent search, or when Korean biotech/pharma assignees are relevant.

---

## Date Field Definitions

Always distinguish between these three dates:

| Field | Meaning |
|---|---|
| Filing date (출원일) | Date application was submitted |
| Publication date (공개일) | Date application was published (usually 18 months after filing) |
| Grant date (등록일) | Date patent was granted |

Never conflate these. If only one date is available, label it clearly.

---

## Number Type Definitions

| Type | Example | Meaning |
|---|---|---|
| Patent number (등록번호) | US12345678B2 | Granted patent |
| Application number (출원번호) | US18/123456 | Filed application |
| Publication number (공개번호) | US20260123456A1 | Published application |

Never mix these types. If uncertain which type a number is, label as "Number type: unverified".
