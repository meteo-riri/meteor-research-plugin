# meteor-research — Meteor Biotech Research Plugin

Claude Code plugin for biomedical research: literature search, spatial omics monitoring,
reviewer response drafting, and patent landscape search.

---

## Data Policy Notice

> **Before using this plugin with internal data:**
> Do not include in any query or input:
> - Confidential project names or unpublished targets
> - Unpublished experimental results
> - Patent-sensitive claim concepts
> - Patient identifiers or clinical data
> - Partner or third-party confidential information
>
> Unless explicitly approved for external model processing by Meteor Biotech IT/Legal.
>
> Do NOT use a personal Claude account for confidential company materials.

---

## Installation

### Step 1 — Add Meteor Biotech marketplace (admin, one-time)

```bash
/plugin marketplace add meteo-riri/meteor-research-plugin
```

### Step 2 — Install (each team member)

```bash
/plugin install meteor-research@meteorbiotech
```

Or from the shell:
```bash
claude plugin install meteor-research@meteorbiotech
```

### Local development / testing (no install required)

```bash
# Load plugin for current session only
claude --plugin-dir ~/projects/meteor-research-plugin

# After editing files, reload without restarting
/reload-plugins

# Validate structure before pushing
claude plugin validate .
```

---

## Commands

All commands use the `meteor-research:` namespace:

| Command | Description |
|---|---|
| `/meteor-research:search-paper <query>` | Search PubMed, bioRxiv, Europe PMC |
| `/meteor-research:monitor-spatial` | Check latest spatial omics papers (v0.2) |
| `/meteor-research:draft-response` | Draft reviewer response letter |
| `/meteor-research:search-patent <query>` | Patent landscape search (v0.3, experimental) |

Skills also auto-trigger from natural language (except `monitor-spatial`).

---

## Features

### v0.1 — Available now

#### `search-paper`
Searches PubMed, Europe PMC, bioRxiv, and publisher sites. Returns verified results
with evidence levels, summaries, and DOI validation. Results are ranked by relevance.

```
/meteor-research:search-paper MERFISH spatial transcriptomics breast cancer
```

Results are displayed by default. Use "save results" to write to `papers/`.

#### `draft-response`
Drafts point-by-point reviewer response letters. Detects manuscript files in the
current directory, or accepts pasted reviewer comments. Uses `[AUTHOR INPUT REQUIRED]`
placeholders for missing information — never fabricates experiments or statistics.

```
/meteor-research:draft-response
```

### v0.2 — Spatial Omics Monitor (coming)

#### `monitor-spatial`
Checks PubMed + bioRxiv + medRxiv for new spatial omics papers since the last run.
Maintains a local state file in `.meteor-research/` to track seen papers.

> **Note on scheduling:** Monitoring state is project-local. Running from different
> repositories creates separate histories. For a shared team history, designate one
> repository as the canonical monitor home.
>
> Cloud Routines (`/schedule`) run on a fresh repo clone — state is NOT persisted
> across Routine runs. Routine reports are stateless weekly digests.
> Requires Pro/Max/Team/Enterprise plan; may be disabled by org admin.

### v0.3 — Patent Search (coming, experimental)

#### `search-patent`
Technology landscape search across USPTO (PatentsView), Google Patents, Espacenet,
and KIPRIS. All results include legal disclaimers. Not a legal opinion.

---

## Anti-Fabrication Policy

This plugin enforces strict rules to prevent AI hallucination:

- Every paper result must be retrieved from PubMed, Europe PMC, bioRxiv, or Crossref
- DOIs are never guessed — missing DOIs are shown as "Not available"
- Paper summaries are never generated without an abstract
- Reviewer responses never claim experiments were performed unless confirmed
- Patent claim text is never inferred from snippets

If the mandatory policy file cannot be loaded, all skills abort with an error.

---

## Version History

See [CHANGELOG.md](CHANGELOG.md).

---

## Updating the Plugin

After a new version is pushed to the repository:

```bash
# Team members update
/plugin marketplace update meteorbiotech

# Or reinstall
/plugin install meteor-research@meteorbiotech
```

**Release process (for maintainers):**
1. Bump version in `.claude-plugin/plugin.json` and `.claude-plugin/marketplace.json`
2. Update `CHANGELOG.md`
3. `git tag v0.x.y && git push --tags`

---

## Troubleshooting

**Plugin not loading:**
```bash
claude plugin validate .
```

**Commands not found after update:**
```
/reload-plugins
```

**PubMed returning no results:** The NCBI API occasionally rate-limits. The plugin
automatically falls back to Europe PMC. If both fail, results from bioRxiv are shown.
