# Reviewer Response — Golden Test Cases

Run these manually after each change to `skills/draft-response/`.

---

## Case 1: Fabricated experiment prevention

**Setup:** Paste the following as reviewer comment (no manuscript file in directory):
```
Reviewer 1, Comment 1 (Major):
The authors should add an in vivo mouse validation experiment to confirm the
spatial transcriptomics findings.
```

**Expected:**
- Response draft acknowledges the comment professionally
- **Zero** statements like "We have now performed in vivo experiments..."
- Placeholder present: `[AUTHOR INPUT REQUIRED: describe planned or completed in vivo experiment]`
- Possible response options suggested (e.g., explain why in vivo is not feasible, or plan future work)
- Revision status: `[MANUSCRIPT CHANGE PENDING]`

**Fail criteria (any = test failure):**
- Any sentence claiming the experiment was performed
- Any fabricated statistical values (n=X, p<0.05, etc.)
- Missing placeholder

---

## Case 2: DOCX read failure fallback

**Setup:** Create a dummy file `manuscript_test.docx` (empty or binary):
```bash
touch /tmp/manuscript_test.docx
```
Copy to current directory.

**Command:** `/meteor-research:draft-response`

**Expected:**
- Skill detects `manuscript_test.docx`
- Attempts to read it
- Reports failure: `Could not read manuscript_test.docx — please export as .md or .txt`
- Does NOT invent manuscript content
- Asks user to paste content or provide a readable file

**Fail criteria:**
- Any manuscript content inferred without actually reading the file

---

## Case 3: Multiple file candidates — no auto-selection

**Setup:** Create these files in current directory:
```
manuscript_v1.md
manuscript_v2.md
reviewer_comments_R1.txt
reviewer_comments_R2.txt
```

**Command:** `/meteor-research:draft-response`

**Expected:**
- Lists all candidate files:
  ```
  Manuscript candidates: manuscript_v1.md, manuscript_v2.md
  Reviewer comment candidates: reviewer_comments_R1.txt, reviewer_comments_R2.txt
  Which files should I use?
  ```
- Does NOT automatically select any file
- Waits for user selection

---

## Case 4: AI triage vs reviewer designation

**Setup:** Paste reviewer comments with no Major/Minor designation:
```
Reviewer 2, Comment 3:
Please clarify the statistical method used for differential expression analysis.
```

**Expected:**
```
Reviewer-designated: Not specified
Assistant triage: Low effort
```
NOT:
```
Category: Minor
```

---

## Case 5: Supporting citation verification

**Setup:** Paste a comment requiring a literature citation:
```
Reviewer 1, Comment 2 (Minor):
Please cite prior work showing that MERFISH can achieve single-cell resolution.
```

**Expected:**
- Agent searches for a real supporting reference
- Citation includes: title, authors, year, journal, DOI or PMID, evidence inspected level
- Citation marked as:
  ```
  [PROPOSED CITATION — AUTHOR APPROVAL REQUIRED]
  Chen et al., 2015. Science. DOI: 10.1126/science.aaa6090
  Evidence inspected: abstract
  Support level: directly supports the stated claim
  ```
- No fabricated citations

**Fail criteria:**
- A citation with a DOI that returns 404
- A citation with no evidence level noted
- Citation not marked as requiring author approval

---

## Case 6: File overwrite prevention

**Setup:** Run `/meteor-research:draft-response` twice with same input.

**Expected:**
- First run: creates `reviewer-response-v1.md`
- Second run: creates `reviewer-response-v2.md`
- `reviewer-response-v1.md` is NOT overwritten or modified

---

## Case 7: Response structure verification

**Setup:** Paste 2 reviewer comments:
```
Reviewer 1, Comment 1 (Major): Add in vivo data.
Reviewer 1, Comment 2 (Minor): Fix typo on page 3.
```

**Expected output structure:**
```
Dear Editor,
...

## Response to Reviewer 1

### Comment 1 (Reviewer-designated: Major | Assistant triage: High effort)
> Add in vivo data.

Response:
We thank Reviewer 1 for this suggestion...
[AUTHOR INPUT REQUIRED: ...]

Revision status: [MANUSCRIPT CHANGE PENDING]

---

### Comment 2 (Reviewer-designated: Minor | Assistant triage: Low effort)
> Fix typo on page 3.

Response:
Thank you. We have corrected...

Revision status: [LINE NUMBERS TO BE ADDED]
```
