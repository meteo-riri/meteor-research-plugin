# Changelog

## [0.3.1] — 2026-08-07

### Fixed
- `search-patent`: PatentsView API retired (USPTO migration March 2026, now requires API key)
  — rewritten to use Google Patents WebSearch as primary, with WebFetch for page details
  — patent-databases.md updated with accurate access status for all sources
  — USPTO ODP documented as optional (API key required)
- `brainstorm`: PubMed literature anchoring was missing EFetch step — now does full
  ESearch→EFetch batch to retrieve abstracts before generating ideas
- `search-paper`: bioRxiv window was hardcoded to 30 days — now adaptive
  (30 days for "recent/latest" queries, 90 days for general queries)
- `draft-experiment`: removed unused `Agent` from allowed-tools
- `monitor-spatial`: bioRxiv local filtering now explicitly uses Group A OR Group B keywords

## [0.3.0] — 2026-08-07

### Added
- `search-patent` skill: technology landscape search across patent databases
  - PatentsView (USPTO) granted patents and published applications via structured API
  - Google Patents via WebSearch + WebFetch
  - Espacenet (European/international patents)
  - KIPRIS (Korean patents, on request)
  - Evidence tiers: [claim-inspected] / [official-page] / [snippet-only]
  - Independent claim retrieval with plain-language orientation summary
  - Number conflict detection (cross-source disagreement flagged, never silently merged)
  - Mandatory legal disclaimer on all output
  - Filing / publication / grant date clearly distinguished

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
