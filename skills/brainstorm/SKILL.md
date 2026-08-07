---
name: brainstorm
description: >
  Brainstorm research ideas, hypotheses, and analysis directions for a given topic.
  Triggers: "브레인스토밍", "연구 아이디어", "분석 방향", "어떤 방향으로 연구할까",
  "research ideas for X", "brainstorm X", "hypothesis generation",
  "가설 생성", "연구 주제 추천", "다음 실험 방향".
  Do NOT trigger for specific paper summaries or protocol drafting.
argument-hint: "<research topic, dataset, observation, or open question>"
allowed-tools: Read, WebFetch, WebSearch, Write
---

# brainstorm

## Step 0 — Load context

Read `${CLAUDE_PLUGIN_ROOT}/references/anti-fabrication.md`.
Apply fabrication rules: do NOT present speculative ideas as established facts.
All brainstormed ideas are explicitly labeled as hypotheses or suggestions, not conclusions.

---

## Step 1 — Understand the starting point

Extract from `$ARGUMENTS` or conversation:
- Core topic or observation that prompted brainstorming
- What is already known or done (if mentioned)
- Constraints or preferences (technique, model system, timeline)
- Goal type: hypothesis generation / analysis direction / experiment ideas / study design

If unclear, ask once:
> "What is the starting observation or question you want to explore?"

---

## Step 2 — Literature anchoring (brief)

Run a quick search to anchor brainstorming in real published work:
```
https://eutils.ncbi.nlm.nih.gov/entrez/eutils/esearch.fcgi
  ?db=pubmed&term={TOPIC}&retmax=5&retmode=json&sort=pub_date
```
Retrieve titles and abstracts for top 3–5 results.
Use these to:
- Identify what has already been done (avoid duplication)
- Surface gaps in the field
- Suggest directions building on recent work

Report any referenced paper with `[abstract]` or `[title-only]` evidence tag.
Do NOT cite papers not retrieved. Do NOT summarize papers without abstracts.

---

## Step 3 — Generate ideas

Structure output into three tiers:

### Tier 1 — Incremental / Near-term
Ideas that extend current work with available tools and data.
- Feasible in current lab setting
- Low methodological risk
- Builds directly on existing findings

### Tier 2 — Exploratory / Medium-term
Ideas that require new techniques, collaborations, or data acquisition.
- Higher novelty, higher effort
- 6–18 month horizon
- Flag key unknowns: `[KEY UNKNOWN: whether X is feasible]`

### Tier 3 — Speculative / Long-term
Forward-looking ideas; may require technology not yet mature.
- Label explicitly: `[SPECULATIVE — requires validation]`
- Do NOT present as actionable without caveats

---

## Step 4 — For each idea, provide

```
### Idea {N}: {Short title}
Tier: {1 / 2 / 3}

**Core concept:** One sentence.

**Rationale:** Why this is interesting — grounded in existing knowledge or data.

**Key experiment / analysis:** What would you actually do?

**Expected challenge:** Main technical or conceptual hurdle.

**If successful, impact:** What would this finding enable?
```

Maximum 8 ideas total (2–3 per tier). Quality over quantity.

---

## Step 5 — Prioritization suggestion

After listing all ideas, suggest a starting point:
> "Suggested starting point: Idea {N} — {reason: lowest risk / highest impact / most feasible given stated constraints}"

This is a suggestion only — clearly labeled as such.

---

## Step 6 — Save (optional)

Only save if user explicitly requests.
File naming: `brainstorm-{sanitized-topic}-{YYYY-MM-DD}.md`
If exists: `-2`, `-3` suffix. Never overwrite.

---

## Output framing rules

- All ideas are hypotheses or proposals — never present as established findings
- Distinguish clearly: "Published evidence suggests X" vs. "One hypothesis is Y"
- If an idea requires a specific assumption, state it explicitly
- Avoid overpromising: "This could reveal..." not "This will show..."
