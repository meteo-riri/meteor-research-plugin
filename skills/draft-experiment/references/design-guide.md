# Experimental Design Guide

## Core Principles

1. **Controls first** — every experiment needs positive and negative controls defined before methods
2. **One variable at a time** — flag if multiple variables are being changed simultaneously
3. **Pre-register outcomes** — define what "success" looks like before running the experiment
4. **Reproducibility** — minimum 3 biological replicates for in vitro; more for in vivo

## Common Study Types and Minimum Requirements

### In vitro (cell-based)
- Biological replicates: ≥3 independent experiments
- Technical replicates: ≥2–3 per experiment
- Controls: untreated, vehicle (solvent), positive control
- Cell line authentication: STR profiling recommended

### In vivo (animal)
- IACUC/IACEC approval required — flag with `[REGULATORY CLEARANCE REQUIRED]`
- Power calculation mandatory before finalizing n
- Randomization and blinding plan
- Humane endpoints defined

### Patient / clinical samples
- IRB approval required — flag with `[REGULATORY CLEARANCE REQUIRED]`
- Consent documentation
- De-identification plan
- Sample QC criteria (tumor purity, RIN, etc.)

### Multi-omics / sequencing
- Library prep QC checkpoints (RIN, fragment size, concentration)
- Sequencing depth per sample (context-dependent)
- Batch effect plan (randomize samples across batches)
- Data storage and sharing plan (GDPR/HIPAA if applicable)

## Standard Workflow Structure

```
Sample preparation
  ↓
Treatment / perturbation
  ↓
Sample collection (define timepoints)
  ↓
QC checkpoint
  ↓
Assay (primary readout)
  ↓
Validation assay (orthogonal method)
  ↓
Data analysis
  ↓
Statistical testing (pre-specify test)
```

## Statistical Considerations

Always flag these for author confirmation:
- Primary statistical test (t-test, ANOVA, mixed model, etc.)
- Multiple testing correction (Bonferroni, FDR, etc.)
- Effect size and power assumptions for sample size calculation
- One-sided vs. two-sided test

Never suggest a specific n without user-provided effect size and power inputs.

## Common Pitfalls to Flag

- Missing vehicle control (solvent/carrier effects)
- Technical replicates mistaken for biological replicates
- Pseudoreplication in single-cell or spatial data
- Batch effects not accounted for
- Primary antibody not validated for species/application
