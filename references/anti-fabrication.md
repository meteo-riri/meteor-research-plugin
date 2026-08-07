# Anti-Fabrication Rules — Mandatory Policy

**This file is the single authoritative source of anti-fabrication rules for all
skills and agents in the meteor-research plugin. All skills and agents must load
and follow this file before generating any output.**

If this file cannot be loaded, STOP immediately and report:
> "Mandatory plugin policy could not be loaded. No literature, citation,
> patent, or reviewer-response output was generated."
Do NOT proceed with the task. **Fail closed.**

---

## Core Rules

### 1. Never Generate or Guess

NEVER generate, invent, or guess:
- Paper titles, authors, journals, or publication dates
- DOIs, PMIDs, PMCIDs, bioRxiv IDs, or any identifiers
- Abstract or results content not retrieved from an authoritative source
- Experiment outcomes, statistical values, p-values, n numbers, effect sizes
- Gene names, protein interactions, or biological claims not in the source
- Patent numbers, claim text, assignee names, or legal status

### 2. Every Result Must Be Sourced

Every paper or patent in the output MUST be:
- Retrieved from an authoritative source (PubMed, Europe PMC, bioRxiv API,
  PatentsView, or an official patent office page)
- Accompanied by the source URL or API endpoint used
- Tagged with an evidence level (see below)

### 3. DOI Rules

- Use ONLY DOIs present in retrieved metadata, exactly as given
- If no DOI exists in the metadata: output `DOI: Not available`
- NEVER construct, guess, or abbreviate a DOI string
- NEVER confirm a DOI from a web search snippet alone

### 4. Zero Results

When a search returns zero results:
- State `No results found for: <query>` clearly
- Suggest 2–3 alternative queries
- Do NOT fill the output with plausible-sounding papers or patents

### 5. API or Access Failures

When an API returns an error, rate-limit, or incomplete data:
- Report the failure explicitly: `PubMed API unavailable — falling back to Europe PMC`
- Do NOT substitute inferred or invented content for missing data
- Try the next source in the priority list; if all fail, report to the user

### 6. Evidence Level Tags (required on every entry)

Every paper or patent entry must carry exactly one tag:

| Tag | Meaning |
|---|---|
| `[title-only]` | Only bibliographic metadata retrieved (no abstract) |
| `[abstract]` | Abstract text retrieved and inspected |
| `[full-text]` | Article body (Methods/Results) inspected — landing page or snippet does NOT qualify |
| `[partial-text]` | Article partially retrieved |
| `[snippet-only]` | Web search snippet only — may appear in "Unverified candidates" section only; no summary, no DOI generation |

### 7. Result Tiers

Outputs must be separated into tiers:

- **Verified results** — metadata confirmed by PubMed / Europe PMC / bioRxiv / Crossref → included in main table (top 10)
- **Provisional results** — publisher page confirmed but primary DB not yet verified → separate section
- **Unverified candidates** — `[snippet-only]` only → separate section, no summary, not in top 10

### 8. No Fabricated Reviewer Content

In `draft-response`:
- NEVER state that an experiment, analysis, figure, table, or manuscript revision
  was completed unless explicitly confirmed by user-provided text
- NEVER fabricate: p-values, n numbers, effect sizes, gene names, citations
- NEVER infer manuscript content from reviewer phrasing alone

When evidence is missing, use ONLY these placeholders:
```
[AUTHOR INPUT REQUIRED: <description of what is needed>]
[EXPERIMENT NOT YET CONFIRMED]
[LINE NUMBERS TO BE ADDED]
[FIGURE TO BE ADDED: <describe what figure should show>]
[STATISTICAL VALUES TO BE VERIFIED BY AUTHOR]
[MANUSCRIPT CHANGE PENDING]
```

### 9. No Fabricated Patent Content

In `search-patent`:
- NEVER guess or construct patent numbers, claim text, or legal status
- If claim text cannot be retrieved: output `Claim text: Not available. Could not be retrieved.`
- Do NOT infer claim orientation from a snippet alone
- Mark all results with the Search date: `Search performed on: YYYY-MM-DD`

---

## Data Policy

Do not include in any query or input:
- Confidential project names or unpublished targets
- Unpublished experimental results
- Patent-sensitive claim concepts
- Patient identifiers or clinical data
- Partner or third-party confidential information

Unless explicitly approved for external model processing by Meteor Biotech IT/Legal.
