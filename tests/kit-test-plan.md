# ai-sdlc-kit Test Plan

This document defines the test scenarios for validating the ai-sdlc-kit framework across all supported flow paths.

## Purpose

The kit has been built and refined iteratively. The existing example (`example-01-generic-service`) covers only one path: greenfield, happy path, no re-entry, no addendums. This test plan defines scenarios that cover the remaining permutations to verify that specs, prompts, validators, and playbook instructions are coherent across all paths.

## How to Use This Test Plan

Each scenario is a walk-through of a specific flow path. To execute a scenario:

1. Read the scenario description and preconditions
2. Follow the file sequence, providing the listed inputs to each prompt/validator
3. Verify expected behavior at each step
4. Record the result (PASS/FAIL) and any issues found

Scenarios can be executed manually (human follows playbook with AI) or by an AI assistant walking through the instructions.

---

## Part 1: Structural Integrity Checks

These checks verify that the kit's files are internally consistent — no broken references, no missing files, no contradictions between documents.

### S-01: File Reference Integrity

**What:** Every file reference in prompts, validators, specs, playbook, and READMEs points to a file that exists.

**How:** Scan all `.md` files for references to other `.md` files. Verify each referenced file exists.

**Files:**
- All files in `docs/prompts/`, `docs/validators/`, `docs/specs/`, `docs/artifacts/`
- `docs/playbook.md`, `docs/index.md`, `docs/how-to-adapt.md`, `docs/how-to-use-with-ai.md`

**Pass criteria:** Zero broken references.

---

### S-02: Four-File Completeness

**What:** Every governed artifact type has all four files: spec, template, prompt, validator.

**How:** For each artifact type (PRD, ACF, SAD, DCF, TDD, WDD, ORD), confirm the existence of `{type}-spec.md`, `{type}-template.md`, `{type}-prompt.md`, `{type}-validator.md`.

**Expected artifact types and their four files:**

| Type | Spec | Template | Prompt | Validator |
|------|------|----------|--------|-----------|
| PRD | `prd-spec.md` | `prd-template.md` | `prd-prompt.md` | `prd-validator.md` |
| ACF | `acf-spec.md` | `acf-template.md` | `acf-prompt.md` | `acf-validator.md` |
| SAD | `sad-spec.md` | `sad-template.md` | `sad-prompt.md` | `sad-validator.md` |
| DCF | `dcf-spec.md` | `dcf-template.md` | `dcf-prompt.md` | `dcf-validator.md` |
| TDD | `tdd-spec.md` | `tdd-template.md` | `tdd-prompt.md` | `tdd-validator.md` |
| WDD | `wdd-spec.md` | `wdd-template.md` | `wdd-prompt.md` | `wdd-validator.md` |
| ORD | `ord-spec.md` | `ord-template.md` | `ord-prompt.md` | `ord-validator.md` |

**Additional governed checks (spec + validator, no template/prompt):**

| Type | Spec | Validator | Notes |
|------|------|-----------|-------|
| DoR | `dor-spec.md` | `dor-validator.md` | Validates WDD work items, not a generated artifact |
| Consistency | `consistency-spec.md` | `consistency-validator.md` | Has a prompt (`consistency-prompt.md`) but no template |
| Execution | `execution-spec.md` | — | Defines phase rules, no validator (phases use per-phase prompts) |

**Pass criteria:** All expected files exist. No artifact type is missing a file.

---

### S-03: Hard Gate Alignment

**What:** Hard gates listed in each spec match the hard gates evaluated by the corresponding validator.

**How:** For each artifact type, extract the hard gate list from `{type}-spec.md` and compare it to the hard gates evaluated in `{type}-validator.md`. They must match exactly — same names, same count.

**Artifact types to check:** PRD, ACF, SAD, DCF, TDD, WDD, DoR, ORD, Consistency

**Pass criteria:** Every spec's hard gate list matches its validator's hard gate list exactly.

---

### S-04: Template Section Alignment

**What:** Template sections match the content rules defined in the corresponding spec.

**How:** For each artifact type, extract the section headings from `{type}-template.md` and compare them to the sections defined in `{type}-spec.md`. Every spec section that defines content rules should have a corresponding template section.

**Artifact types to check:** PRD, ACF, SAD, DCF, TDD, WDD, ORD

**Pass criteria:** No spec section is missing from the template. No template section is absent from the spec.

---

### S-05: Prompt-Spec Reference Consistency

**What:** Every prompt references its corresponding spec, and the spec reference is accurate.

**How:** For each prompt, verify it contains a SPEC REFERENCE section pointing to the correct `{type}-spec.md`.

**Prompts to check:** All prompts in `docs/prompts/` (artifact generation prompts and execution phase prompts)

**Pass criteria:** Every prompt references the correct spec. No prompt references a nonexistent spec.

---

### S-06: Playbook-Spec Consistency

**What:** The playbook's descriptions of each step (inputs, outputs, gates, checks) match the corresponding specs.

**How:** For each playbook step (Steps 1-10, Step 6a), verify:
- Listed inputs match what the prompt accepts
- Listed validator matches the correct validator file
- Listed checks/gates match the spec's hard gates
- Descriptions match the spec's content rules

**Pass criteria:** No contradictions between playbook descriptions and spec definitions.

---

### S-07: README Index Completeness

**What:** Each directory README lists all files in that directory.

**How:** For each README (`docs/artifacts/README.md`, `docs/prompts/README.md`, `docs/validators/README.md`, `docs/specs/README.md`), compare the files listed in the README against the actual files in the directory.

**Pass criteria:** Every file in each directory is listed in its README. No README lists a file that doesn't exist.

---

### S-08: Intake Template Completeness

**What:** Every intake template referenced by a prompt actually exists and has the expected structure.

**How:** Verify:
- `product-brief-template.md` exists and is referenced by `prd-prompt.md`
- `architecture-context-template.md` exists and is referenced by `acf-prompt.md`
- `design-context-template.md` exists and is referenced by `dcf-prompt.md`
- `system-context-template.md` exists and is referenced by `sad-prompt.md`

**Pass criteria:** All intake templates exist and are referenced correctly.

---

### S-09: Codebase Analysis Output Alignment

**What:** The codebase analysis prompt's output sections align with the intake templates they claim to fill.

**How:** Compare:
- Output A structure against `architecture-context-template.md` sections
- Output B description against `design-context-template.md` sections
- Output C description against `system-context-template.md` sections

**Pass criteria:** Each output can fill its target intake template without structural mismatch.

---

### S-10: Consistency Spec Check Count

**What:** The consistency spec's traceability checks match what the playbook and validator expect.

**How:** Count checks in `consistency-spec.md`, compare to:
- Playbook Step 6a check list
- `consistency-validator.md` hard gate list

**Pass criteria:** All three sources agree on the number and names of checks.

---

## Part 2: Flow Scenarios

Each scenario defines a specific path through the kit. Scenarios are designed so that together they cover all major dimensions.

### Coverage Matrix

| Dimension | F-01 | F-02 | F-03 | F-04 | F-05 | F-06 | F-07 | F-08 | F-09 | F-10 |
|-----------|------|------|------|------|------|------|------|------|------|------|
| Greenfield | x | | x | x | x | | x | x | | x |
| Brownfield | | x | | | | x | | | x | |
| New ACF/DCF | x | x | | | | | | | x | |
| Reuse ACF/DCF | | | x | | | | | | | |
| Validator PASS 1st try | x | x | | | | | | | | |
| Validator FAIL + fix | | | x | | | | | | | |
| Repeated failure → re-entry | | | | x | | | | | | |
| No addendums | x | x | x | | | x | x | x | x | |
| Addendum + cascade | | | | | x | | | | | |
| Re-entry w/ impact analysis | | | | x | | | | | | |
| AI-assigned work items | x | x | x | x | x | x | | | x | x |
| Human-assigned work items | | | | | | | x | | | |
| Sequential execution | x | x | x | x | x | x | x | | x | |
| Parallel execution | | | | | | | | x | | |
| Phase 3 happy path | x | x | | | | x | | x | x | |
| Phase 3 escalation (3 failures) | | | | | | | | | | x |
| Phase 3 context recovery | | | x | | | | | | | |
| Single work group | | x | | | | | x | | | |
| Multiple work groups + gates | x | | x | x | x | x | | x | x | |
| Consistency PASS | x | x | | | | x | x | x | x | |
| Consistency FAIL | | | | | x | | | | | |
| Work item cancellation | | | | | | | | | | x |

---

### F-01: Greenfield Happy Path (Baseline)

**What:** Complete end-to-end flow for a new project. No failures, no re-entry, no addendums. This is the simplest path and should be validated first.

**Preconditions:** No existing codebase, no existing ACF or DCF.

**Flow:**

| Step | Action | Files Used | Inputs | Expected Output | Verify |
|------|--------|-----------|--------|-----------------|--------|
| 1 | Human writes product brief | `product-brief-template.md` | Human knowledge | Completed product brief | Brief follows template structure |
| 2 | Generate PRD | `prd-spec.md` + `prd-prompt.md` + `prd-template.md` | Product brief | Generated PRD | PRD follows template |
| 3 | Validate PRD | `prd-spec.md` + `prd-validator.md` | Generated PRD | PASS JSON | All hard gates pass |
| 4 | Freeze PRD | — | — | Frozen PRD | — |
| 5 | Human fills architecture context | `architecture-context-template.md` | Human knowledge | Completed intake form | Form follows template |
| 6 | Generate ACF | `acf-spec.md` + `acf-prompt.md` + `acf-template.md` | Architecture context intake | Generated ACF | ACF follows template |
| 7 | Validate ACF | `acf-spec.md` + `acf-validator.md` | Generated ACF | PASS JSON | All hard gates pass |
| 8 | Freeze ACF | — | — | Frozen ACF | — |
| 9 | Generate SAD | `sad-spec.md` + `sad-prompt.md` + `sad-template.md` | Frozen PRD + Frozen ACF | Generated SAD | Intent verification in §1, traces to PRD |
| 10 | Validate SAD | `sad-spec.md` + `sad-validator.md` | Generated SAD | PASS JSON | All hard gates pass |
| 11 | Freeze SAD | — | — | Frozen SAD | — |
| 12 | Human fills design context | `design-context-template.md` | Human knowledge | Completed intake form | Form follows template |
| 13 | Generate DCF | `dcf-spec.md` + `dcf-prompt.md` + `dcf-template.md` | Design context intake | Generated DCF | DCF follows template |
| 14 | Validate DCF | `dcf-spec.md` + `dcf-validator.md` | Generated DCF | PASS JSON | All hard gates pass |
| 15 | Freeze DCF | — | — | Frozen DCF | — |
| 16 | Generate TDD | `tdd-spec.md` + `tdd-prompt.md` + `tdd-template.md` | Frozen SAD + Frozen DCF | Generated TDD | Intent verification in §1, traces to SAD |
| 17 | Validate TDD | `tdd-spec.md` + `tdd-validator.md` | Generated TDD | PASS JSON | All hard gates pass |
| 18 | Freeze TDD | — | — | Frozen TDD | — |
| 19 | Generate WDD | `wdd-spec.md` + `wdd-prompt.md` + `wdd-template.md` | Frozen TDD | Generated WDD | Intent verification in §1, traces to TDD |
| 20 | Validate WDD | `wdd-spec.md` + `wdd-validator.md` | Generated WDD | PASS JSON | All hard gates pass |
| 21 | Validate DoR (per item) | `dor-spec.md` + `dor-validator.md` | Each WDD work item | PASS JSON per item | All items pass DoR |
| 22 | Consistency check | `consistency-spec.md` + `consistency-prompt.md` | All frozen artifacts + validated WDD | Consistency report | All 8 checks addressed |
| 23 | Validate consistency | `consistency-spec.md` + `consistency-validator.md` | Consistency report | PASS JSON | All hard gates pass |
| 24 | Freeze WDD | — | — | Frozen WDD | — |
| 25 | Generate execution plan | `execution-plan-prompt.md` | Frozen WDD + TDD + ACF + DCF | Execution order + context files | All items sequenced, context extracted |
| 26 | Assemble Phase 1 prompts | `execution-plan-tests-prompt.md` | Execution plan | Ready-to-use test prompts | One prompt per work item |
| 27 | Execute Phase 1 (per item) | `test-prompt.md` (assembled) | WDD item + TDD + DCF context | Approved test specs | Tests map to acceptance criteria |
| 28 | Assemble Phase 2 prompts | `execution-plan-plan-prompt.md` | Execution plan + Phase 1 output | Ready-to-use plan prompts | Includes test specs |
| 29 | Execute Phase 2 (per item) | `plan-prompt.md` (assembled) | Test specs + TDD contracts + source | Approved implementation plan | Files identified, risks noted |
| 30 | Assemble Phase 3 prompts | `execution-plan-code-prompt.md` | Execution plan + Phase 1 + Phase 2 output | Ready-to-use code prompts | Includes tests and plan |
| 31 | Execute Phase 3 (per item) | `code-prompt.md` (assembled) | Plan + tests + source | Passing tests + code | Completion verification passes |
| 32 | Assemble Phase 4 prompts | `execution-plan-review-prompt.md` | Execution plan + Phase 3 output | Ready-to-use review prompts | Includes diff and test results |
| 33 | Execute Phase 4 (per item) | `review-prompt.md` (assembled) | Code + tests + WDD item | Review verdict | Scope, correctness, coverage checked |
| 34 | Work group gate | — | Test results + reviews | Gate file recorded | All items in group complete |
| 35 | Generate ORD | `ord-spec.md` + `ord-prompt.md` + `ord-template.md` | Frozen TDD + ACF + DCF | Generated ORD | Evidence-based verification |
| 36 | Validate ORD | `ord-spec.md` + `ord-validator.md` | Generated ORD | PASS JSON | All hard gates pass |

**Key verifications:**
- Intent verification appears in SAD §1, TDD §1, WDD §1
- No scope expansion at any step
- Traceability: PRD requirements → SAD components → TDD modules → WDD items

---

### F-02: Brownfield with Codebase Analysis

**What:** Adding a feature to an existing codebase. Uses codebase analysis to pre-fill intake templates.

**Preconditions:** Existing codebase accessible to AI. No existing ACF or DCF.

**Flow:**

| Step | Action | Files Used | Inputs | Expected Output | Verify |
|------|--------|-----------|--------|-----------------|--------|
| 1 | Run codebase analysis | `codebase-analysis-prompt.md` | Existing codebase | 4 outputs (A, B, C, D) | Outputs fill template structures |
| 2 | Human reviews Output A | `architecture-context-template.md` | Output A (pre-filled) | Reviewed architecture context | Human corrected any inaccuracies |
| 3 | Human reviews Output B | `design-context-template.md` | Output B (pre-filled) | Reviewed design context | Human corrected any inaccuracies |
| 4 | Human reviews Output C | `system-context-template.md` | Output C (pre-filled) | Reviewed system context | Human corrected any inaccuracies |
| 5 | Human reviews Output D | — | Ambiguities report | Resolved ambiguities | Human addressed gaps |
| 6 | Human writes product brief | `product-brief-template.md` | Human knowledge | Completed product brief | Describes the change, not the whole system |
| 7 | Generate PRD | `prd-spec.md` + `prd-prompt.md` + `prd-template.md` | Product brief | Generated PRD | Scoped to the change |
| 8 | Validate + Freeze PRD | `prd-spec.md` + `prd-validator.md` | Generated PRD | PASS → Freeze | — |
| 9 | Generate ACF | `acf-spec.md` + `acf-prompt.md` + `acf-template.md` | Reviewed architecture context + org standards | Generated ACF | Reflects existing tech stack |
| 10 | Validate + Freeze ACF | `acf-spec.md` + `acf-validator.md` | Generated ACF | PASS → Freeze | — |
| 11 | Generate SAD | `sad-spec.md` + `sad-prompt.md` + `sad-template.md` | Frozen PRD + Frozen ACF + reviewed system context | Generated SAD | Describes existing architecture relevant to change |
| 12 | Validate + Freeze SAD | `sad-spec.md` + `sad-validator.md` | Generated SAD | PASS → Freeze | — |
| 13 | Generate DCF | `dcf-spec.md` + `dcf-prompt.md` + `dcf-template.md` | Reviewed design context + org standards | Generated DCF | Reflects existing conventions |
| 14 | Validate + Freeze DCF | `dcf-spec.md` + `dcf-validator.md` | Generated DCF | PASS → Freeze | — |
| 15–36 | TDD → WDD → Execute → ORD | (same as F-01 steps 16-36) | — | — | Same verifications as F-01 |

**Key verifications:**
- Codebase analysis Output A fills `architecture-context-template.md` structure
- Codebase analysis Output B fills `design-context-template.md` structure
- Codebase analysis Output C fills `system-context-template.md` structure
- SAD describes existing system architecture, not a new design from scratch
- ACF reflects discovered technology constraints, not just organizational standards

---

### F-03: Reuse Existing ACF/DCF + Validator Failure Fix Cycle

**What:** A project where the organization already has frozen ACF and DCF from a prior project. TDD validator fails on first attempt and requires a fix cycle. Phase 3 requires context recovery (fresh session).

**Preconditions:** Frozen ACF and DCF from a prior project. No existing codebase.

**Flow:**

| Step | Action | Files Used | Inputs | Expected Output | Verify |
|------|--------|-----------|--------|-----------------|--------|
| 1-4 | PRD: Generate → Validate → Freeze | (same as F-01) | Product brief | Frozen PRD | — |
| 5 | Skip ACF generation | — | Existing frozen ACF | — | Playbook says "skip this step and use the existing ACF" |
| 6 | Skip DCF generation | — | Existing frozen DCF | — | Playbook says "skip this step and use the existing DCF" |
| 7 | Generate SAD | (same as F-01) | Frozen PRD + existing ACF | Generated SAD | Uses existing ACF constraints |
| 8 | Validate + Freeze SAD | (same as F-01) | — | Frozen SAD | — |
| 9 | Generate TDD | `tdd-spec.md` + `tdd-prompt.md` + `tdd-template.md` | Frozen SAD + existing DCF | Generated TDD | Uses existing DCF standards |
| 10 | Validate TDD (1st attempt) | `tdd-spec.md` + `tdd-validator.md` | Generated TDD | **FAIL** JSON | Blocking issues identified |
| 11 | Fix blocking issues | — | Validator output | Updated TDD | Only blocking issues fixed |
| 12 | Validate TDD (2nd attempt) | `tdd-spec.md` + `tdd-validator.md` | Updated TDD | PASS JSON | All hard gates pass |
| 13 | Freeze TDD | — | — | Frozen TDD | — |
| 14-24 | WDD → Consistency → Freeze → Execution Plan | (same as F-01) | — | — | — |
| 25 | Execute Phase 1 + Phase 2 (per item) | (same as F-01) | — | Approved tests + plan | — |
| 26 | Execute Phase 3 — session runs out of context | `code-prompt.md` | Plan + tests + source | Partial progress, session ends | — |
| 27 | Context recovery: start fresh session | `code-prompt.md` | Context file + current files + last test output + attempt summary | Resumed Phase 3 | New session has full context |
| 28 | Phase 3 completes | — | — | Passing tests | Completion verification passes |
| 29-36 | Phase 4 → ORD | (same as F-01) | — | — | — |

**Key verifications:**
- Existing ACF/DCF are accepted without regeneration
- Validator FAIL produces actionable `blocking_issues` with gate, description, location
- Fix cycle: only blocking issues are fixed, not warnings
- Re-validation uses a fresh session (not the same session that fixed the artifact)
- Context recovery follows the 5-step protocol from the playbook

---

### F-04: Re-entry Protocol with Impact Analysis

**What:** A requirement change after TDD freeze forces re-entry to the PRD. Impact analysis is run before committing to re-entry. Downstream artifacts are cascade-revalidated.

**Preconditions:** All artifacts frozen through WDD. Execution has not started.

**Flow:**

| Step | Action | Files Used | Inputs | Expected Output | Verify |
|------|--------|-----------|--------|-----------------|--------|
| 1-24 | Full greenfield flow through WDD freeze | (same as F-01) | — | All artifacts frozen | — |
| 25 | Business requirement changes | — | — | Human identifies PRD needs updating | — |
| 26 | Run impact analysis | `impact-analysis-prompt.md` | Proposed change + all frozen artifacts | Impact report | Identifies affected downstream artifacts |
| 27 | Validate impact analysis | `impact-analysis-validator.md` | Impact report | PASS JSON | Report is complete |
| 28 | Human decides to proceed with re-entry | — | Impact report | Decision to re-enter PRD | — |
| 29 | Unfreeze PRD | — | — | PRD editable | Only PRD unfrozen |
| 30 | Update PRD | `prd-prompt.md` or manual edit | Change description | Updated PRD | Change incorporated |
| 31 | Re-validate PRD | `prd-spec.md` + `prd-validator.md` | Updated PRD | PASS JSON | — |
| 32 | Re-freeze PRD | — | — | Frozen PRD (v2) | — |
| 33 | Cascade: re-validate SAD | `sad-spec.md` + `sad-validator.md` | SAD against updated PRD | PASS or FAIL | Check if SAD still aligns |
| 34 | If SAD FAIL: update + re-validate + re-freeze SAD | — | — | Frozen SAD (v2) | — |
| 35 | Cascade: re-validate TDD | `tdd-spec.md` + `tdd-validator.md` | TDD against updated SAD | PASS or FAIL | — |
| 36 | If TDD FAIL: update + re-validate + re-freeze TDD | — | — | Frozen TDD (v2) | — |
| 37 | Cascade: re-validate WDD | `wdd-spec.md` + `wdd-validator.md` | WDD against updated TDD | PASS or FAIL | — |
| 38 | If WDD FAIL: update + re-validate + re-freeze WDD | — | — | Frozen WDD (v2) | — |
| 39 | Re-run consistency check | `consistency-spec.md` + `consistency-prompt.md` | All updated frozen artifacts | Updated consistency report | All 8 checks pass |
| 40 | Continue to execution | — | — | — | — |

**Key verifications:**
- Impact analysis identifies all affected downstream artifacts before re-entry
- Only the highest affected artifact is unfrozen (not all downstream preemptively)
- Cascade validation proceeds top-down: SAD → TDD → WDD
- Consistency check is re-run after cascade completes
- Re-entry does not expand scope beyond the original intent

---

### F-05: Addendum with Downstream Cascade

**What:** After TDD freeze, a design clarification requires a TDD addendum. The addendum triggers downstream revalidation. Consistency check catches an issue.

**Preconditions:** All artifacts frozen through WDD. Execution has not started.

**Flow:**

| Step | Action | Files Used | Inputs | Expected Output | Verify |
|------|--------|-----------|--------|-----------------|--------|
| 1-24 | Full greenfield flow through WDD freeze | (same as F-01) | — | All artifacts frozen | — |
| 25 | Design clarification needed for TDD | — | — | Human identifies TDD needs addendum | — |
| 26 | Create TDD addendum | Manual | Clarification details | TDD addendum document | Addendum does not contradict frozen TDD |
| 27 | Validate TDD addendum | `tdd-spec.md` + `tdd-validator.md` | TDD + addendum | PASS JSON | Addendum is valid |
| 28 | Freeze TDD addendum | — | — | Frozen TDD addendum | — |
| 29 | Re-validate WDD against TDD + addendum | `wdd-spec.md` + `wdd-validator.md` | WDD + TDD + addendum | PASS or FAIL | Check if WDD still aligns |
| 30 | Re-run consistency check | `consistency-spec.md` + `consistency-prompt.md` | All frozen artifacts + addendums | Consistency report | Check 7 (Addendum Integration) verifies addendum reflected |
| 31 | Consistency check returns FAIL | `consistency-spec.md` + `consistency-validator.md` | Consistency report | FAIL JSON | Addendum not fully reflected in WDD |
| 32 | Fix WDD to reflect addendum | — | — | Updated WDD | — |
| 33 | Re-validate WDD | `wdd-spec.md` + `wdd-validator.md` | Updated WDD | PASS | — |
| 34 | Re-validate DoR (affected items) | `dor-spec.md` + `dor-validator.md` | Affected WDD items | PASS per item | — |
| 35 | Re-run consistency check | (same as step 30) | All artifacts + addendums | PASS JSON | All 8 checks pass |
| 36 | Re-freeze WDD | — | — | Frozen WDD (v2) | — |
| 37 | Continue to execution | — | — | — | — |

**Key verifications:**
- Addendum is validated against the same spec as the base artifact
- Consistency check 7 (Addendum Integration) catches unintegrated changes
- WDD is updated to reflect the addendum before re-freeze
- DoR is re-run for affected work items

---

### F-06: Brownfield with Reused ACF/DCF

**What:** Adding a feature to an existing codebase where the organization already has ACF and DCF. Tests that brownfield codebase analysis + reused context files work together.

**Preconditions:** Existing codebase. Frozen ACF and DCF from a prior project.

**Flow:**

| Step | Action | Files Used | Inputs | Expected Output | Verify |
|------|--------|-----------|--------|-----------------|--------|
| 1 | Run codebase analysis | `codebase-analysis-prompt.md` | Existing codebase | 4 outputs | — |
| 2 | Human reviews Output C only | `system-context-template.md` | Output C (pre-filled) | Reviewed system context | Only system context needed (ACF/DCF reused) |
| 3 | Human notes Output D ambiguities | — | Output D | Resolved ambiguities | — |
| 4-6 | PRD: Generate → Validate → Freeze | (same as F-01) | Product brief | Frozen PRD | — |
| 7 | Skip ACF generation | — | Existing frozen ACF | — | Verify codebase analysis Output A was reviewed against existing ACF for any contradictions |
| 8 | Generate SAD | `sad-spec.md` + `sad-prompt.md` + `sad-template.md` | Frozen PRD + existing ACF + reviewed system context | Generated SAD | Uses both existing ACF and brownfield system context |
| 9 | Validate + Freeze SAD | — | — | Frozen SAD | — |
| 10 | Skip DCF generation | — | Existing frozen DCF | — | Verify codebase analysis Output B was reviewed against existing DCF for any contradictions |
| 11-36 | TDD → WDD → Execute → ORD | (same as F-01) | — | — | — |

**Key verifications:**
- Codebase analysis outputs are reviewed even when ACF/DCF are reused (to catch contradictions)
- System context from codebase analysis feeds SAD generation alongside existing ACF
- SAD describes existing architecture relevant to the change

---

### F-07: Human-Assigned Work Items

**What:** A WDD contains human-assigned work items (infrastructure, deployment verification) that follow the verification checklist model instead of the AI execution loop.

**Preconditions:** Greenfield project. WDD contains both AI-assigned and human-assigned items.

**Flow:**

| Step | Action | Files Used | Inputs | Expected Output | Verify |
|------|--------|-----------|--------|-----------------|--------|
| 1-24 | Full flow through WDD freeze | (same as F-01) | — | Frozen WDD with mixed assignment types | At least one item has Assignee Type "Human" |
| 25 | Generate execution plan | `execution-plan-prompt.md` | Frozen WDD + TDD + ACF + DCF | Execution order | Human items included in order, dependencies respected |
| 26 | Execute AI-assigned items | (same as F-01 execution loop) | — | Completed AI items | Tests → Plan → Code → Review |
| 27 | Execute human-assigned item | — | WDD item | Human performs work | No AI execution loop |
| 28 | Human records evidence | — | Acceptance criteria | Evidence per checklist item | Screenshots, logs, command output |
| 29 | Human peer reviews evidence | — | Evidence + WDD item | Review approval | Another human reviews |
| 30 | Work group gate | — | All items (AI + human) complete | Gate file | All items in group complete |

**Key verifications:**
- Human-assigned items follow verification checklist model (not Given/When/Then)
- Human-assigned items still follow execution plan order
- Human-assigned items still respect dependency chains
- Human-assigned items still require evidence and DoD satisfaction
- Work group gate includes both AI and human items

---

### F-08: Parallel Execution

**What:** Multiple work items within the same work group are marked parallel-safe and executed concurrently.

**Preconditions:** Greenfield project. WDD has a work group with at least 2 items that share no file-level conflicts.

**Flow:**

| Step | Action | Files Used | Inputs | Expected Output | Verify |
|------|--------|-----------|--------|-----------------|--------|
| 1-25 | Full flow through execution plan | (same as F-01) | — | Execution plan with parallel-safe items marked | — |
| 26 | Verify parallel safety in plan | — | Execution plan | Items marked parallel-safe | No file overlap between parallel items |
| 27 | Execute Phase 1 in parallel | `test-prompt.md` (assembled) | Multiple items simultaneously | Test specs per item | Separate sessions, no context bleed |
| 28 | Execute Phase 2 in parallel | `plan-prompt.md` (assembled) | Multiple items simultaneously | Plans per item | Plans don't overlap files |
| 29 | Execute Phase 3 in parallel | `code-prompt.md` (assembled) | Multiple items simultaneously | Code per item | No merge conflicts |
| 30 | Execute Phase 4 in parallel | `review-prompt.md` (assembled) | Multiple items simultaneously | Reviews per item | Each review scoped to its item |
| 31 | Work group gate | — | All parallel items complete | Gate file | All PRs merged |

**Key verifications:**
- Execution plan explicitly marks items as parallel-safe vs. sequential
- Parallel items touch different files (no file-level conflicts)
- Each parallel item runs in a separate AI session
- Work group gate waits for all parallel items to complete

---

### F-09: Brownfield End-to-End (Full Codebase Analysis Flow)

**What:** Complete brownfield flow emphasizing the codebase analysis → intake template → artifact generation chain. Tests the full brownfield-specific path.

**Preconditions:** Existing codebase. No existing ACF or DCF.

**Flow:**

| Step | Action | Files Used | Inputs | Expected Output | Verify |
|------|--------|-----------|--------|-----------------|--------|
| 1 | Run codebase analysis | `codebase-analysis-prompt.md` | Existing codebase | Output A, B, C, D + Summary table | All 4 outputs present |
| 2 | Verify Output A fills architecture-context-template | `architecture-context-template.md` | Output A | Pre-filled form | Completeness checklist items marked |
| 3 | Verify Output B fills design-context-template | `design-context-template.md` | Output B | Pre-filled form | Sections filled where data available |
| 4 | Verify Output C fills system-context-template | `system-context-template.md` | Output C | Pre-filled form | Completeness checklist items marked |
| 5 | Verify Output D reports ambiguities | — | Output D | Ambiguity list | Inconsistencies, gaps, unclear boundaries flagged |
| 6 | Verify summary table | — | Summary | Status per output | Complete/Partial status with notes |
| 7 | Human reviews and edits all 3 intake forms | — | Outputs A, B, C | Reviewed intake forms | Human corrected inaccuracies, filled gaps |
| 8 | Human resolves Output D ambiguities | — | Output D | Decisions made | Ambiguities resolved before proceeding |
| 9 | PRD: Generate → Validate → Freeze | (same as F-01) | Product brief | Frozen PRD | — |
| 10 | ACF: Generate from reviewed Output A | `acf-spec.md` + `acf-prompt.md` + `acf-template.md` | Reviewed architecture context | Generated ACF | Reflects existing tech stack |
| 11 | Validate + Freeze ACF | — | — | Frozen ACF | — |
| 12 | SAD: Generate with system context | `sad-spec.md` + `sad-prompt.md` + `sad-template.md` | Frozen PRD + Frozen ACF + reviewed system context | Generated SAD | Describes existing architecture |
| 13 | Validate + Freeze SAD | — | — | Frozen SAD | — |
| 14 | DCF: Generate from reviewed Output B | `dcf-spec.md` + `dcf-prompt.md` + `dcf-template.md` | Reviewed design context | Generated DCF | Reflects existing conventions |
| 15 | Validate + Freeze DCF | — | — | Frozen DCF | — |
| 16-36 | TDD → WDD → Execute → ORD | (same as F-01) | — | — | — |

**Key verifications:**
- Codebase analysis produces all 4 outputs with correct structure
- Each output fills its corresponding intake template accurately
- Human review is required before using pre-filled forms as generation input
- SAD receives system context as additional input (beyond PRD + ACF)
- Generated artifacts reflect the existing codebase, not a greenfield design

---

### F-10: Phase 3 Escalation + Work Item Cancellation

**What:** During Phase 3, the same test fails 3 times and escalates to the human. A work item is cancelled mid-execution, and dependent items are assessed.

**Preconditions:** Greenfield project. Execution in progress. At least one work item has a downstream dependency.

**Flow:**

| Step | Action | Files Used | Inputs | Expected Output | Verify |
|------|--------|-----------|--------|-----------------|--------|
| 1-25 | Full flow through execution plan | (same as F-01) | — | Execution plan | — |
| 26 | Execute Phase 1 + Phase 2 for Item A | (same as F-01) | — | Approved tests + plan | — |
| 27 | Phase 3 attempt 1: tests fail | `code-prompt.md` | Plan + tests + source | Test failure | Failure mapped to acceptance criterion |
| 28 | Phase 3 attempt 2: same test fails | `code-prompt.md` | Updated code + test output | Same test failure | Structured failure feedback: what failed, what criterion, expected vs. actual |
| 29 | Phase 3 attempt 3: same test fails | `code-prompt.md` | Updated code + test output | Same test failure | 3rd attempt for same failure |
| 30 | Escalation | — | Structured report | Human receives escalation | Report includes: what failed, what was tried, why it didn't work |
| 31 | Human decides to cancel Item A | — | — | Cancellation with recorded reason | — |
| 32 | Assess dependency impact | — | WDD dependency chain | Impact assessment | Items depending on Item A identified |
| 33 | Dependent Item B assessed | — | — | Decision: cancel, modify, or proceed | Human decides |
| 34 | If Item B proceeds: verify no broken dependency | — | Item B's inputs | Confirmed inputs available | Item B can execute without Item A |
| 35 | Continue execution for remaining items | (same as F-01) | — | — | — |

**Key verifications:**
- Each failed attempt maps failure to acceptance criterion (structured failure feedback)
- Escalation triggers after 3 attempts for the same failure (not 3 total failures)
- Escalation report is structured: what failed, what was tried, why it didn't work
- Cancellation includes a recorded reason
- Dependency chain is assessed after cancellation
- Remaining items continue unaffected (if no dependency on cancelled item)

---

## Part 3: Execution Checklist

Use this checklist to track which tests have been run and their results.

### Structural Integrity

| Check | Status | Issues Found |
|-------|--------|--------------|
| S-01: File Reference Integrity | PASS | Zero broken references across 41 files, 58 cross-references verified |
| S-02: Four-File Completeness | PASS (fixed) | Originally FAIL: impact analysis had no spec. Fixed by creating `impact-analysis-spec.md`, updating prompt and validator to reference it. |
| S-03: Hard Gate Alignment | PASS | All 9 spec-validator pairs have identical hard gate lists |
| S-04: Template Section Alignment | PASS | All 7 template-spec pairs have matching sections |
| S-05: Prompt-Spec Reference Consistency | PASS | 13/13 artifact+execution+governance prompts reference correct spec. 6 assembly/utility prompts correctly have no spec reference. |
| S-06: Playbook-Spec Consistency | PASS | All 10 verification items pass. Note: playbook Phase 3 Context Recovery section extends beyond execution-spec (operational guidance, not a mismatch). |
| S-07: README Index Completeness | PASS | All 4 directory READMEs list all files: artifacts (12/12), prompts (19/19), validators (10/10), specs (11/11) |
| S-08: Intake Template Completeness | PASS | All 4 intake templates exist and are correctly referenced by their prompts |
| S-09: Codebase Analysis Output Alignment | PASS | Output A embeds architecture-context-template structure exactly. Outputs B and C reference their templates by name with "use exact template structure" instruction. |
| S-10: Consistency Spec Check Count | PASS | All 3 sources agree: spec (8 checks + report_completeness), playbook (8 checks listed), validator (8 + report_completeness = 9 hard gates) |

### Flow Scenarios

| Scenario | Status | Issues Found |
|----------|--------|--------------|
| F-01: Greenfield Happy Path | | |
| F-02: Brownfield with Codebase Analysis | | |
| F-03: Reuse ACF/DCF + Validator Fix Cycle | | |
| F-04: Re-entry with Impact Analysis | | |
| F-05: Addendum with Cascade | | |
| F-06: Brownfield with Reused ACF/DCF | | |
| F-07: Human-Assigned Work Items | | |
| F-08: Parallel Execution | | |
| F-09: Brownfield End-to-End | | |
| F-10: Phase 3 Escalation + Cancellation | | |

---

## Priority Order

Run tests in this order for maximum early signal:

1. **Structural checks first** (S-01 through S-10) — these are fast, mechanical, and catch broken plumbing
2. **F-01: Greenfield Happy Path** — validates the core flow works end-to-end
3. **F-02: Brownfield with Codebase Analysis** — validates the brownfield additions
4. **F-03: Reuse ACF/DCF + Fix Cycle** — validates validator failure handling
5. **F-04: Re-entry Protocol** — validates the most complex governance path
6. **Remaining scenarios** — in any order, based on which paths your organization uses most
