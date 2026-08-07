# Supporting Reference Rules — Reviewer Response

## When to Add a Citation

Add a supporting citation ONLY when:
1. The reviewer explicitly requests a citation for a specific claim
2. The response makes a claim that directly depends on published evidence
3. A well-known precedent exists that directly validates the method used

Do NOT add citations:
- Speculatively ("here's a related paper that might help")
- To support claims about experiments in the current manuscript
- Based only on a web search snippet

## Verification Steps (all required)

For every proposed citation:
1. Retrieve metadata: title, authors, year, journal, DOI or PMID
2. Retrieve and read the abstract (minimum)
3. Confirm the paper actually supports the stated claim
4. Use WebFetch or PubMed EFetch to verify — do not rely on snippet text alone

## Required Label

All proposed citations must be labeled:
```
[PROPOSED CITATION — AUTHOR APPROVAL REQUIRED]
{Authors}. "{Title}." {Journal} ({Year}). DOI: {DOI}
Evidence inspected: {abstract | full-text | title-only}
Support level: {directly supports claim | methodological precedent | general context}
```

"Directly supports claim" means the paper's findings directly validate the specific
statement being made in the response. "Methodological precedent" means the paper
validates the approach but not necessarily the exact finding. "General context"
means it provides background but does not directly support the claim.

## Quantity Limit

Maximum **3 unsolicited new citations** per response section.
If a reviewer asks for more, note: `[ADDITIONAL CITATIONS MAY BE NEEDED — AUTHOR TO REVIEW]`

## Do Not Cite These

- Papers you cannot retrieve (no abstract = no citation)
- Papers that support a fabricated experimental claim
- Papers where title or DOI does not match after verification
