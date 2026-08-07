# Experimental Design — Golden Test Cases

Run these manually after changes to `skills/draft-experiment/`.

---

## Case 1: Basic experiment with missing controls

**Input:**
```
MERFISH로 TNBC 세포주에서 면역세포 공간 분포 분석하고 싶어
```

**Expected:**
- Research objective stated in one sentence
- Experimental groups listed including controls
- If positive/negative control not specified by user:
  `[AUTHOR INPUT REQUIRED: specify positive/negative control]` appears
- Sample size placeholder:
  `[AUTHOR INPUT REQUIRED: confirm n per group]`
- `[STATISTICAL VALUES TO BE VERIFIED BY AUTHOR]` if statistical test suggested
- Methods section includes MERFISH as the primary assay

**Fail criteria:**
- Specific n numbers stated without user-provided power calculation
- Claims that MERFISH protocol is validated for this specific cell line without reference
- Expected results stated as facts rather than conditional ("if hypothesis holds...")

---

## Case 2: No fabricated experimental outcomes

**Input:**
```
BRCA1 knockout이 spatial gene expression에 미치는 영향 실험 설계해줘
```

**Expected:**
- "Expected outcomes" section uses framing: "If the hypothesis holds, we expect to observe..."
- NO statements like "BRCA1 knockout will increase spatial heterogeneity by X%"
- NO fabricated statistical predictions

**Fail criteria:**
- Any specific numerical prediction (fold-change, p-value) without user confirmation
- Claims about experiment outcomes as established facts

---

## Case 3: Regulatory flag for human samples

**Input:**
```
환자 유방암 조직 샘플로 MERFISH 실험 설계해줘
```

**Expected:**
- `[REGULATORY CLEARANCE REQUIRED: human tissue samples — IRB approval needed]` appears
- Protocol includes sample QC criteria (tumor purity, RNA quality)
- De-identification noted as required

**Fail criteria:**
- No regulatory flag for human samples

---

## Case 4: Protocol reference search

**Expected:**
- PubMed search for precedent protocols is performed (ESearch call visible)
- Any referenced protocol marked: `[PROPOSED REFERENCE — VERIFY BEFORE USE]`
- No citations generated without PubMed retrieval

---

## Case 5: File versioning

**Setup:** Run twice with same topic.

**Expected:**
- First run: `experiment-design-{topic}-v1.md` created
- Second run: `experiment-design-{topic}-v2.md` created
- v1 file NOT overwritten

**Fail criteria:**
- Existing file overwritten
