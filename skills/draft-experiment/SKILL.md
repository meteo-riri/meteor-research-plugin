---
name: draft-experiment
description: >
  Draft an experimental design or protocol outline for a research project.
  Triggers: "실험 계획 짜줘", "프로토콜 초안 작성", "experiment design",
  "draft protocol", "실험 설계해줘", "어떤 실험을 해야 할까",
  "실험 방법 제안해줘", "design an experiment for X".
  Do NOT trigger for simple methodological questions already answered in conversation.
argument-hint: "<research question, hypothesis, or experimental goal>"
allowed-tools: Read, WebFetch, WebSearch, Write
---

# draft-experiment

## Step 0 — Load mandatory policy

Read `${CLAUDE_PLUGIN_ROOT}/references/anti-fabrication.md`.
Read `<skill-dir>/references/safety-rules.md`.

If either file cannot be loaded, STOP and report:
> "Mandatory policy files could not be loaded. Experiment drafting aborted."

---

## Step 1 — Understand the research goal

Extract from `$ARGUMENTS` or conversation:
- Research question or hypothesis
- Biological system (cell line, organism, tissue, patient samples)
- Available techniques or resources (if mentioned)
- Constraints (timeline, budget, sample availability — if mentioned)

If the research question is unclear, ask once:
> "What is the main experimental question or hypothesis you want to address?"

---

## Step 2 — Literature check (optional but recommended)

If the topic is specific enough, do a brief PubMed search for precedent protocols:
```
https://eutils.ncbi.nlm.nih.gov/entrez/eutils/esearch.fcgi
  ?db=pubmed&term={TOPIC}+protocol+OR+{TOPIC}+method&retmax=5&retmode=json&sort=pub_date
```
Use only to identify established methodological precedents — not to generate novel claims.
Report any precedent found with `[PROPOSED REFERENCE — VERIFY BEFORE USE]`.

---

## Step 3 — Draft experimental design

Use `<skill-dir>/references/design-guide.md` for structure.

Produce the following sections:

### 3a. Research Objective
One sentence: what question does this experiment answer?

### 3b. Experimental Groups
List all groups including:
- Experimental conditions
- Positive control
- Negative control
- Vehicle/mock control (if applicable)

**NEVER omit controls without flagging:** if controls are unclear, use:
`[AUTHOR INPUT REQUIRED: specify positive/negative control]`

### 3c. Key Variables
- Independent variable(s)
- Dependent variable(s) / readout(s)
- Confounding variables to account for

### 3d. Sample Size & Replicates
- Biological replicates: `[AUTHOR INPUT REQUIRED: confirm n per group]`
- Technical replicates
- **NEVER suggest specific n without statistical justification** — use placeholder unless user provides power calculation inputs

### 3e. Proposed Methods / Assays
List assays in order of workflow. For each:
- Method name
- Purpose
- Key parameters (use `[TO BE OPTIMIZED]` for conditions not yet established)
- Reference protocol if found in Step 2

### 3f. Timeline (optional)
Only if user provides duration constraints. Use weeks/days, not specific dates.

### 3g. Expected Outcomes (conditional)
**NEVER predict specific results.** Only describe:
- What each readout will look like IF the hypothesis is correct
- What would falsify the hypothesis

Use framing: "If the hypothesis holds, we expect to observe..."

---

## Step 4 — Identify gaps and risks

List:
- Missing information that must be confirmed before proceeding
- Technical risks (e.g., antibody validation, cell line authentication)
- Regulatory considerations (if animal work, patient samples, GMO)

---

## Step 5 — Save

File naming: `experiment-design-{sanitized-topic}-v1.md`
If exists: `-v2`, `-v3` — never overwrite.

After saving:
```
Saved: experiment-design-{topic}-v1.md
Controls specified: {yes / [AUTHOR INPUT REQUIRED]}
Sample size confirmed: {yes / [AUTHOR INPUT REQUIRED]}
Placeholders requiring input: {N}
Proposed references: {N} (need verification)
```

---

## Data policy reminder

Experimental design may contain unpublished hypotheses and proprietary targets.
Confirm this is approved for external model processing under Meteor Biotech's data policy.
