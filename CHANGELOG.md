# Changelog

## [0.2.0] — 2026-08-07

### Added
- `monitor-spatial` skill: monitors PubMed, bioRxiv, medRxiv for new spatial omics papers
  - State-based deduplication (`.meteor-research/spatial-monitor-state.json`)
  - 48 core technology keywords across MERFISH, Visium, seqFISH, CODEX, Slide-seq, etc.
  - Topic grouping by application area (TME, neuroscience, immunology, methods, etc.)
  - Malformed state recovery with `.bak` file
  - Cloud Routine mode (stateless 7-day digest)
- `draft-experiment` skill: experimental design and protocol outline drafting
  - Structured output: objective, groups, controls, variables, methods, timeline
  - Mandatory placeholder system for missing controls/sample sizes
  - Literature precedent search via PubMed
  - Safety rules preventing fabricated experimental outcomes
- `brainstorm` skill: research idea and analysis direction generation
  - Three-tier output: incremental / exploratory / speculative
  - Literature anchoring via PubMed before generating ideas
  - Clear hypothesis labeling — never presented as established findings

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
