# Patent Databases — API Reference

## Access Status (as of 2026-08-07)

| Database | Access Method | Status |
|---|---|---|
| Google Patents | WebSearch + WebFetch | WebSearch ✓ / WebFetch may be blocked |
| Espacenet (EPO) | WebSearch + WebFetch | WebSearch ✓ / WebFetch 403 common |
| KIPRIS (Korean) | WebSearch | ✓ snippet-only |
| USPTO PatentsView | API (key required) | Migrated March 2026 — key required |

---

## 1. Google Patents (Primary)

### Search
```
WebSearch: site:patents.google.com {QUERY}
```
Returns snippets with patent number, title, assignee, date (when visible).

### Individual page fetch
```
WebFetch: https://patents.google.com/patent/{NUMBER}/en
```
If successful, extract from page:
- Title, assignee, inventors
- Filing date / Publication date / Grant date (all three, distinguished)
- Abstract
- Claims section (look for "Claims" heading → Claim 1 text)

If blocked → mark `[snippet-only]`, do NOT infer claims.

### Number formats
| Format | Example | Type |
|---|---|---|
| US{N}B2 | US12345678B2 | Granted US patent |
| US{N}A1 | US20260123456A1 | Published US application |
| WO{YYYY}{N}A1 | WO2026012345A1 | PCT international application |
| EP{N}A1 | EP3456789A1 | European application |
| EP{N}B1 | EP3456789B1 | Granted European patent |

NEVER construct or guess patent numbers. Use only numbers retrieved from sources.

---

## 2. Espacenet (EPO)

### Search
```
WebSearch: site:worldwide.espacenet.com {QUERY}
```
or
```
WebSearch: espacenet {QUERY} patent
```

Direct fetch attempts:
```
WebFetch: https://worldwide.espacenet.com/patent/search?q={URL_ENCODED_QUERY}
```
Note: Espacenet often returns 403. Fall back to WebSearch snippets if blocked.

**When to prioritize:** European inventors/assignees, EP or WO patents, PCT applications.

---

## 3. KIPRIS (Korean Intellectual Property)

### Search
```
WebSearch: site:patent.kipris.or.kr {QUERY}
WebSearch: KIPRIS 특허 {QUERY_IN_KOREAN}
```

Results almost always `[snippet-only]`. Korean patent numbers: KR{N}B1 (granted), KR{N}A (application).

**When to search:** User explicitly requests Korean patent search, or Korean biotech company assignees are relevant.

---

## 4. USPTO Open Data Portal (PatentsView replacement)

PatentsView migrated to https://data.uspto.gov in March 2026. Requires API key.

**API key registration:** https://data.uspto.gov/apis/getting-started

**Endpoint (if key available):**
```
GET https://data.uspto.gov/api/v1/patent/search
  ?q={QUERY}
  Authorization: Bearer {API_KEY}
```

Fields changed from old PatentsView:
- `patent_number` → `patent_id`
- `per_page` → `size` (max 1000)
- Paging: `after` cursor instead of `page`

**Default behavior in plugin:** Skip if no key. Note in output: `USPTO ODP not searched — API key required`.

---

## Date Field Definitions

Always distinguish:

| Field | Korean | Meaning |
|---|---|---|
| Filing date | 출원일 | Date application submitted |
| Publication date | 공개일 | ~18 months after filing, when published |
| Grant date | 등록일 | Date patent was granted |

If only one date is available from a source, label it clearly — do NOT assume which type it is.

---

## Claim Evidence Rules

| Tag | Requirement |
|---|---|
| `[claim-inspected]` | Claim 1 text read from official page |
| `[official-page]` | Abstract retrieved from official page, no claim text |
| `[snippet-only]` | Only search snippet available; no official page accessed |

NEVER generate a "claim orientation summary" from `[snippet-only]` results.
NEVER guess claim text from abstract or title.
