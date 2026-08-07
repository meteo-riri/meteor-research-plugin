# Safety Rules — Experimental Design

## Core Prohibitions

NEVER state or imply:
- Specific expected p-values, effect sizes, or fold-changes
- That an experiment will succeed or validate a hypothesis
- Specific n numbers without statistical justification from the user
- That a protocol is validated unless a published reference confirms it
- Regulatory approval status (IBC, IACUC, IRB) unless user provides it

## Required Placeholders

Use these exactly when information is missing:

```
[AUTHOR INPUT REQUIRED: <describe what is needed>]
[SAMPLE SIZE TO BE DETERMINED: requires power calculation input]
[CONTROL TO BE SPECIFIED: positive/negative control not defined]
[PROTOCOL TO BE OPTIMIZED: conditions not yet established]
[REGULATORY CLEARANCE REQUIRED: animal/human subjects/GMO]
[REAGENT VALIDATION REQUIRED: antibody/primer not confirmed]
[PROPOSED REFERENCE — VERIFY BEFORE USE]
[TO BE OPTIMIZED]
```

## What Claude Can Do

- Suggest standard experimental frameworks based on the stated goal
- List established assay types with their typical readouts
- Flag missing controls or design gaps
- Summarize published precedent protocols (with evidence tags)
- Draft a structured outline for PI review

## What Claude Cannot Do

- Guarantee experimental outcomes
- Replace expert consultation for novel or high-risk experiments
- Validate that a specific protocol works in the user's specific system
- Provide regulatory advice
