# DPRD Consistency Check — Utility Prompt

## Purpose

Verify that the PRD entering the Engineering Execution Kit is consistent with its upstream source — either the frozen Discovery PRD (Path A) or the Product Brief authorized by the Kit Entry Record (Path B). This check runs at the EEK entry boundary before artifact generation begins.

This is a **utility prompt** — it produces analysis for human review. It does not produce a governed artifact, and its output does not substitute for the Kit Entry Record validation.

## When to Use

Run this prompt:
- **Path A (DPRD entry)**: After placing the frozen DPRD as `docs/sdlc/01-prd.md` and before running the PRD Validator — to confirm the DPRD was placed unmodified.
- **Path B (direct entry)**: After PRD generation — to confirm the generated PRD does not expand scope beyond what the Kit Entry Record authorized and the Product Brief described.

Do not skip this check when taking Path A. The PRD Validator checks content quality; this check verifies provenance integrity.

## Inputs Required

**For Path A (DPRD → PRD):**
1. The frozen Discovery PRD from the Product Intelligence Kit (the source document)
2. The PRD as placed at `docs/sdlc/01-prd.md` in the EEK project (the receiving document)
3. The Kit Entry Record (to confirm Path A was authorized)

**For Path B (Product Brief → PRD):**
1. The Product Brief (the human-authored intake form)
2. The generated PRD (`docs/sdlc/01-prd.md`)
3. The Kit Entry Record (to confirm scope and work type)

## Instructions

### Path A: DPRD Provenance Check

**Step 1: Identity comparison**
Compare the DPRD and the EEK PRD section by section. For each section in the DPRD, confirm it appears verbatim in the PRD. Note any differences, including:
- Added sections or content not in the DPRD
- Removed sections or content from the DPRD
- Modified wording, scope, or requirements
- Reordered sections

**Step 2: Scope boundary check**
Confirm no new goals, requirements, user groups, or acceptance criteria appear in the PRD that were not present in the DPRD.

**Step 3: Document Control verification**
Confirm the PRD's Document Control section references the DPRD ID. Flag if this reference is absent or points to a different artifact.

**Step 4: Verdict**
Render one of:
- `PASS` — PRD is an unmodified copy of the DPRD; no scope additions detected
- `FAIL` — Modifications detected (list each discrepancy with location)
- `WARN` — Minor formatting differences only; no semantic changes; human review advised

### Path B: Product Brief Scope Consistency Check

**Step 1: Scope boundary mapping**
Extract the work scope from the Kit Entry Record (§ Classification Check and §Work Summary). Then identify the scope stated in the Product Brief (problem statement, goals, non-goals, user groups).

**Step 2: PRD scope comparison**
For each goal, requirement, and user group in the generated PRD, confirm it is traceable to the Product Brief or explicitly authorized by the KER scope. Flag any PRD content that:
- Addresses a problem not stated in the Product Brief
- Introduces users or personas not mentioned in the Product Brief
- Adds requirements beyond what the Product Brief described
- Contradicts or weakens the Product Brief's non-goals

**Step 3: KER authorization check**
Confirm the PRD's stated scope is consistent with the work type and authorization recorded in the KER. A KER authorizing a bug fix should not result in a PRD containing feature requirements.

**Step 4: Verdict**
Render one of:
- `PASS` — PRD scope is consistent with Product Brief; no unauthorized expansion detected
- `FAIL` — Scope expansion detected (list each item and location)
- `WARN` — Marginal additions that may be in scope; human judgment required

## Output Format

```
## DPRD Consistency Check (Path A / Path B)

**Artifacts reviewed:**
- Source: {DPRD-ID or Product Brief}
- Receiving: PRD at docs/sdlc/01-prd.md
- KER: {KER-ID}

**Verdict: PASS / FAIL / WARN**

### Findings

| Finding Type | Location | Description |
|-------------|---------|-------------|
| {Addition / Removal / Modification / Expansion} | {§ Section name} | {What changed or was added} |

*If no findings: "No discrepancies detected."*

### Summary

{1–3 sentences describing the overall consistency state. If FAIL, state what must be resolved before proceeding. If WARN, state what the human should review.}
```

## Behavioral Rules

- Do not suggest rewrites or improvements to the PRD — identify discrepancies only.
- Do not evaluate whether the PRD content is good — evaluate whether it is consistent with the source.
- Flag any difference, however small. The human decides whether a difference is material.
- If the path (A or B) cannot be determined from the inputs provided, state this and ask for clarification before proceeding.
- Base analysis only on what is explicitly present in the documents — do not infer scope from context.
