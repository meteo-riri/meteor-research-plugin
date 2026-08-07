# Patent Search Output Schema

## Mandatory Disclaimer (first line of ALL output)

```
⚠️ EXPERIMENTAL. Preliminary landscape search only.
   NOT a legal novelty, FTO, or patentability opinion.
   Patent status, ownership, and claims change frequently.
   Consult a registered patent attorney before any business decision.
   Search date: {YYYY-MM-DD}
```

Never omit this block. Never move it below results.

---

## Per-Result Output Fields

```
### {Title}

| Field | Value |
|---|---|
| Patent number | {number or "Not available"} |
| Application number | {number or "Not available"} |
| Publication number | {number or "Not available"} |
| Assignee | {organization or "Not available"} |
| Inventors | {last names or "Not available"} |
| Filing date | {YYYY-MM-DD or "Not available"} |
| Publication date | {YYYY-MM-DD or "Not available"} |
| Grant date | {YYYY-MM-DD or "Not available"} |
| Jurisdiction | US / EP / WO / KR / other |
| Source | PatentsView / Google Patents / Espacenet / KIPRIS |
| Evidence | [claim-inspected] / [official-page] / [snippet-only] |

**Abstract summary:** {2 sentences max — from retrieved abstract only}

**Independent claim (Claim 1):**
{Claim text verbatim — OR—}
> Claim text: Not available. Could not be retrieved from source.

**Claim orientation (plain language):**
{1 sentence describing what is claimed in lay terms}
{ONLY if claim text was retrieved — never infer from abstract or title}
> If not retrieved: "Not available. Claim text could not be retrieved."

**Patent family / related:** {list related numbers if found, or "Not independently verified"}
**Legal status:** Not independently verified — check official register.
```

---

## Evidence Level Tags

| Tag | Meaning |
|---|---|
| `[claim-inspected]` | Independent claim text retrieved and read |
| `[official-page]` | Official patent page fetched; abstract available but claims not retrieved |
| `[snippet-only]` | Web search snippet only; no official page accessed |

**Rules:**
- `[snippet-only]` results go in a separate "Unverified candidates" section
- Never generate claim orientation from `[snippet-only]` results
- Never confirm a patent number from a snippet alone
- If two sources disagree on patent number → show both, mark conflict

---

## Number Conflict Handling

If Google Patents shows `US12345678B2` but PatentsView shows `US12345679B2` for what appears to be the same patent:
```
⚠️ Number conflict: Google Patents (US12345678B2) vs PatentsView (US12345679B2)
Do not consolidate. Verify with USPTO Patent Full-Text Database.
```
Never arbitrarily pick one number. Show both.

---

## Output Sections

```
## Patent Landscape — {QUERY}
Search date: {YYYY-MM-DD}
Sources: {list}

---
[DISCLAIMER BLOCK]
---

## Granted Patents ({N})
{results}

## Published Applications ({N})
{results}

## Unverified Candidates — [snippet-only] ({N})
Title | Source URL | Note
{list — no abstracts, no claim text, no numbers unconfirmed}

## Search Summary
- Total results: {N granted + N applications + N snippet-only}
- Jurisdictions covered: {list}
- Date range: {oldest} → {newest found}
- Gaps: KIPRIS not searched / Espacenet blocked / etc.
```
