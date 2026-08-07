# Brainstorm — Golden Test Cases

Run these manually after changes to `skills/brainstorm/`.

---

## Case 1: Three-tier output structure

**Input:**
```
spatial omics로 TNBC 연구 방향 브레인스토밍해줘
```

**Expected:**
- Output contains all three tiers: Tier 1 (incremental), Tier 2 (exploratory), Tier 3 (speculative)
- Each idea has: Core concept, Rationale, Key experiment/analysis, Expected challenge, Impact
- Maximum 8 ideas total
- A "Suggested starting point" recommendation at the end

**Fail criteria:**
- Only one or two tiers present
- More than 8 ideas (quality over quantity)
- No starting-point recommendation

---

## Case 2: Literature anchoring — real papers only

**Expected:**
- PubMed ESearch + EFetch batch call made before generating ideas
- At least 1 paper cited with `[abstract]` evidence tag
- No papers cited that were not retrieved from PubMed

**Fail criteria:**
- Papers cited without PubMed retrieval
- Papers cited with `[snippet-only]` tag (insufficient for brainstorm anchoring)
- EFetch skipped — summary generated from title alone

---

## Case 3: Hypothesis vs fact distinction

**Expected in output:**
- Tier 1–2 ideas: "Published evidence suggests X; one direction is to..."
- Tier 3 ideas explicitly labeled: `[SPECULATIVE — requires validation]`
- No idea presented as an established finding

**Fail criteria:**
- Speculative ideas presented as if confirmed
- "This will show..." instead of "This could reveal..."

---

## Case 4: Save only on request

**Input (no save request):**
```
MERFISH 분석 방향 브레인스토밍
```
**Expected:** No file created.

**Input (with save request):**
```
MERFISH 분석 방향 브레인스토밍 — 저장해줘
```
**Expected:** `brainstorm-MERFISH-분석방향-YYYY-MM-DD.md` created.

---

## Case 5: Vague input handling

**Input:**
```
연구 아이디어 줘
```

**Expected:**
- Asks once: "What is the starting observation or question you want to explore?"
- Does NOT generate generic ideas without context
- Does NOT ask again after user provides context

**Fail criteria:**
- Generates ideas without asking for context
- Asks the same clarifying question twice
