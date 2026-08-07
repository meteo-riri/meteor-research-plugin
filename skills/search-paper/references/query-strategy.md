# Query Strategy — Automatic Expansion Rules

Before submitting a user query to any database, apply these expansion rules.

## 1. Technology / Method Aliases

Expand the user's search term to include common aliases:

| User term | Also search |
|---|---|
| spatial transcriptomics | spatial omics, spatial genomics |
| MERFISH | multiplexed error-robust FISH |
| Visium | 10x Visium, Visium HD |
| seqFISH | seqFISH+ |
| Slide-seq | Slide-seqV2 |
| CODEX | CO-Detection by indEXing |
| scRNA-seq | single-cell RNA sequencing, single cell transcriptomics |
| CUT&RUN | CUT&TAG |

## 2. Disease / Tissue Synonyms

| User term | Also search |
|---|---|
| breast cancer | breast tumor, mammary carcinoma |
| lung cancer | lung adenocarcinoma, NSCLC, SCLC |
| TNBC | triple-negative breast cancer |
| glioblastoma | GBM, brain tumor, glioma |

## 3. MeSH Term Addition (PubMed only)

For PubMed queries, append relevant MeSH terms when the query is broad:
- spatial transcriptomics → add `"Gene Expression Profiling"[MeSH]`
- cancer microenvironment → add `"Tumor Microenvironment"[MeSH]`
- single cell → add `"Single-Cell Analysis"[MeSH]`

## 4. Recency

Unless the user specifies a date, default to no date filter for general search.
For "latest" or "recent" queries, use past 12 months.
For monitoring queries (`monitor-spatial`), use past 14 days.

## 5. Do NOT Over-Expand

- Limit OR expansions to 3–4 terms per concept
- Do not add unrelated synonyms speculatively
- If uncertain about an alias, do not include it
