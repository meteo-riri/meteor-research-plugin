# Changelog

## [0.1.0] — 2026-08-07

### Added
- `search-paper` skill: literature search across PubMed, Europe PMC, bioRxiv
  - PubMed 3-step batch API (ESearch → ESummary → EFetch)
  - Europe PMC full-text support
  - bioRxiv/medRxiv date-based search with local keyword filtering
  - Publisher fallback (nature.com, cell.com, science.org)
  - Crossref DOI verification
  - Evidence level tags: [title-only], [abstract], [full-text], [partial-text], [snippet-only]
  - Verified / Provisional / Unverified result tiers
  - Duplicate detection by DOI, PMID, and normalized title
  - Save-on-request only (no automatic file creation)
- `draft-response` skill: point-by-point reviewer response drafting
  - Auto-detects manuscript and reviewer comment files in working directory
  - Multi-file candidate safety: never auto-selects when multiple files match
  - Parallel subagent processing per reviewer (max 3 simultaneous)
  - Mandatory placeholder system for missing experiments/statistics
  - Revision status tracking per response section
  - Supporting citation verification and labeling
  - File overwrite prevention (v1 → v2 → v3 versioning)
- `literature-reviewer` agent: detailed paper analysis (called by search-paper)
- `response-drafter` agent: reviewer response drafting (called by draft-response)
- `references/anti-fabrication.md`: shared mandatory anti-fabrication policy
- Test cases: `tests/paper-search-cases.md`, `tests/reviewer-response-cases.md`

### Pending (v0.2)
- `monitor-spatial` skill with state-based deduplication

### Pending (v0.3)
- `search-patent` skill (experimental)
