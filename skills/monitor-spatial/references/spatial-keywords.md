# Spatial Omics Monitoring Keywords

## Group A — Core Technologies (primary search terms)

Use these as the PubMed query backbone:

```
(
  "spatial transcriptomics" OR "spatial omics" OR "spatial genomics"
  OR "MERFISH" OR "multiplexed error-robust FISH"
  OR "seqFISH" OR "seqFISH+"
  OR "Visium" OR "Visium HD" OR "10x Visium"
  OR "Slide-seq" OR "Slide-seqV2"
  OR "CODEX" OR "CO-Detection by indEXing"
  OR "HDST" OR "STARmap"
  OR "Stereo-seq" OR "Stereo-CITE-seq"
  OR "spatial proteomics" OR "spatial epigenomics"
  OR "in situ sequencing" OR "in situ transcriptomics"
)
```

## Group B — Analysis Methods (secondary, use for local bioRxiv filtering)

Filter bioRxiv results if title or abstract contains:
- spatial domain identification
- spatial clustering
- spatial deconvolution
- spatially variable genes
- cell-cell communication (spatial)
- spatial trajectory
- spatial co-expression
- neighborhood enrichment
- spot deconvolution
- cell type mapping (spatial)

## Group C — Application Areas (tertiary, for topic grouping in output)

| Label | Keywords |
|---|---|
| Tumor microenvironment | tumor, cancer, TME, microenvironment |
| Neuroscience | brain, neuron, cortex, hippocampus, neural |
| Developmental biology | embryo, development, organoid, differentiation |
| Immunology | immune, T cell, macrophage, lymphocyte, inflammation |
| Methods & Tools | benchmark, tool, algorithm, framework, pipeline, software |
| Single-cell integration | single-cell, scRNA-seq, integration, atlas |
| Other | (catch-all) |

## Exclusion Terms

Skip papers where title contains ONLY these without a Group A term:
- FISH (without MERFISH/seqFISH — likely cytogenetics)
- spatial statistics (non-omics context)
- geographic, ecological, epidemiological spatial
