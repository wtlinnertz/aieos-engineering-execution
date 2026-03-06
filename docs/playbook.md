# AI-Assisted Architecture-to-Execution Playbook

This playbook defines a **repeatable, AI-safe process** for moving from **product intent** to **delivered work** across the full Software Development Lifecycle (SDLC).

The playbook is:
- Artifact-centric
- Validator-driven
- Tool-agnostic
- Employer-neutral
- Designed for AI-assisted delivery

---

## Purpose

AI can generate content quickly, but speed without structure leads to:

- Ambiguous requirements
- Scope creep
- Over-design
- Unexecutable work
- Late-stage rework

This playbook ensures that:

- AI output is constrained by intent
- Every handoff is deterministic
- Work is ready before execution
- Humans remain accountable for decisions

---

## Core Principles

These principles apply to every stage:

1. **AI generates, validators decide**
2. **Artifacts are promoted, not rewritten**
3. **Every artifact has a single responsibility**
4. **Execution artifacts are stricter than design artifacts**
5. **Ambiguity is a failure condition**
6. **Non-goals are enforceable**

---

## How to Read This Playbook

This playbook has three parts:

1. **The Process** — The step-by-step flow from product intent to production. Follow this top to bottom for your first project.
2. **The Execution Loop** — The inner loop for implementing each work item. Follow this per WDD item.
3. **Rules and Reference** — Design principles, governance rules, and validator details. Consult as needed.

---

# Part 1: The Process

## Step 0: Kit Entry Gate

**What:** Confirm the work is appropriately routed to EEK, select the entry path, and establish that a priority decision exists before any artifact generation begins.

**Spec:** `docs/specs/kit-entry-spec.md`
**Template:** `docs/artifacts/kit-entry-template.md`
**Validator:** `docs/validators/kit-entry-validator.md`
**Gate:** Validator PASS + human freeze
**Output:** Frozen Kit Entry Record

### Steps

1. Complete `kit-entry-template.md`:
   - Check whether a frozen Work Classification Record exists (from PIK) — if so, reference its ID and confirm it routes to EEK; if not, document why classification was not performed
   - Select exactly one entry path: Path A (DPRD from PIK) or Path B (direct entry)
   - Record the priority decision reference
   - State the scope boundary (in scope and out of scope)
2. Run `kit-entry-validator.md` against the completed record
3. Fix any blocking issues; re-run until PASS
4. Complete the Freeze Declaration
5. Proceed to the PRD Entry Path selected in the record

The Kit Entry Gate is not re-run when re-entering EEK under the Re-entry Protocol — it applies only to initial kit entry.

---

## PRD Entry Paths

The PRD slot is the entry point to this kit. It accepts input from two paths. Both produce a frozen PRD and continue with the same flow.

**Path A — From Product Intelligence Kit (Discovery Entry):**
The Product Intelligence Kit produces a Frozen Discovery PRD (DPRD) as its terminal artifact. The DPRD is delivered to this kit and placed directly as the PRD — no regeneration occurs.

```
Frozen DPRD (from Product Intelligence Kit)
  → Place as docs/sdlc/01-prd.md
  → PRD Acceptance Check (prd-validator.md — confirmation only, no generation)
  → Save result as docs/sdlc/01-prd-validation.json
  → If PASS: freeze PRD slot → continue with ACF step below
  → If FAIL: return failing validator output to PIK team; do not begin ACF
```

**Path B — Direct Entry:**
When no upstream discovery process was used, a human fills the Product Brief intake form and a PRD is generated from it.

```
Product Brief (human intake)
  → PRD (prd-prompt.md)
  → PRD Validator
  → Freeze
  → Continue with ACF step below
```

## Canonical Artifact Flow

After the Kit Entry Record is frozen and the PRD slot is filled (via either path above), the flow is identical:

- Architecture Context (human intake)
  → ACF (`acf-prompt.md`)
  → ACF Validator
  → Freeze

- PRD + ACF
  → SAD (`sad-prompt.md`)
  → SAD Validator
  → Freeze

- Design Context (human intake)
  → DCF (`dcf-prompt.md`)
  → DCF Validator
  → Freeze

- SAD + DCF
  → TDD (`tdd-prompt.md`)
  → TDD Validator
  → Freeze

- TDD
  → WDD (`wdd-prompt.md`)
  → WDD Validator (document-level: scope, structure, granularity)
  → DoR Validator (per work item: readiness, traceability, AI safety)
  → Consistency Check (`consistency-prompt.md` — cross-artifact traceability)
  → Human approval
  → Freeze
  → Execution Plan (`execution-plan-prompt.md`)
  → Execute

- Execute (per work item, in execution plan order):
  → Tests (`test-prompt.md` → human approves specs)
  → Plan (`plan-prompt.md` → human approves plan)
  → Code (`code-prompt.md` → tests pass)
  → Review (`review-prompt.md` → human approves PR)

- Execute (work group complete)
  → Business Acceptance Testing (TBD — process not yet defined)

- Execute (all work items complete)
  → ORD (`ord-prompt.md`)
  → ORD Validator
  → Production Ready

No stage may be skipped. Reusing a frozen ACF or DCF from a prior project satisfies the stage — it does not skip it.

---

## How to Generate an Artifact

Every artifact follows the same generation pattern:

1. **Gather inputs** — Assemble the frozen upstream artifacts listed in the prompt
2. **Use the spec + prompt + template** — Provide the inputs, the corresponding `{type}-spec.md` (content rules), `{type}-prompt.md` (AI instructions), and `{type}-template.md` (structure) to the AI
3. **Run the validator** — Provide the generated artifact and the corresponding `{type}-spec.md` to `{type}-validator.md`
4. **Fix blocking issues only** — If the validator returns FAIL, fix only the blocking issues and re-run
5. **Freeze** — Once the validator returns PASS, the artifact is frozen and may not be changed without re-entry

Each artifact type has four files that work together:

| File | Question | Location |
|------|----------|----------|
| **Spec** (`{type}-spec.md`) | What makes this artifact good? | `docs/specs/` |
| **Template** (`{type}-template.md`) | What does the blank form look like? | `docs/artifacts/` |
| **Prompt** (`{type}-prompt.md`) | How should AI behave when generating? | `docs/prompts/` |
| **Validator** (`{type}-validator.md`) | How to judge pass/fail? | `docs/validators/` |

### How to Run a Validator

Validators are prompts. To run one:

1. Provide the artifact as input to the corresponding `{type}-validator.md`
2. The validator returns structured JSON with `status: PASS | FAIL`
3. If FAIL: review `blocking_issues`, fix them in the artifact, re-run the validator
4. If PASS: the artifact is ready to freeze and promote
5. Do not fix warnings unless they indicate a real problem — warnings are non-blocking

**If the failure is due to incomplete inputs** (e.g., the product brief left decisions unresolved and the PRD correctly marked them as open), the fix belongs in the input, not the artifact. Update the input with the missing decisions, then regenerate the artifact from scratch. This is not re-entry — nothing is frozen yet.

**If the same gate keeps failing** after two fix-and-revalidate cycles, stop and assess:
- If the root cause is in the current artifact, escalate to a human for guidance
- If the root cause is ambiguity or error in an upstream artifact, trigger the Re-entry Protocol

Validators do not redesign or suggest solutions. They evaluate only what is explicitly present.

**Human approval is the final gate.** A validator PASS is necessary but not sufficient. If a human rejects an artifact despite a passing validator, the artifact goes back for revision. The human may see issues — ambiguity, poor judgment, misaligned intent — that a validator cannot catch.

---

## Context Files (ACF and DCF)

The ACF (Architecture Context File) and DCF (Design Context File) are **organizational context** — they define guardrails and standards that apply across projects.

- If your organization already has a frozen ACF or DCF, use it. Do not recreate per project.
- If not, create them as part of your first project and reuse them going forward.
- ACF and DCF have no upstream artifact dependency — they can be created at any time before they are needed.
- ACF must be frozen before SAD generation. DCF must be frozen before TDD generation.
- If your organization has engineering standards or product principles documented (see `docs/principles/`), use them as source material when generating your ACF and DCF. The principles define organizational policy; the ACF and DCF translate that policy into enforceable guardrails for the SDLC flow.

---

## Working with Greenfield and Brownfield Projects

The kit works for both greenfield and brownfield projects. Both paths use the same artifact flow. The difference is in how intake forms are prepared before artifact generation.

### Greenfield Intake Facilitation

For greenfield projects, there is no codebase to analyze. The Architecture Context and Design Context intake forms must be filled from organizational standards, team decisions, and PRD constraints.

Run `greenfield-intake-prompt.md` with the frozen PRD (and any team notes) to facilitate this:

1. **Run greenfield intake** — Provide the frozen PRD to `greenfield-intake-prompt.md`. It extracts explicit PRD constraints into Output A (Architecture Context) and Output B (Design Context), and produces Output C: a consolidated list of every field that requires a team decision, with a specific question for each.
2. **Work through the decisions** — Answer each question in Output C and fill in the corresponding fields in Outputs A and B.
3. **Use the completed forms as inputs** — Feed the completed `architecture-context-template.md` to `acf-prompt.md` and the completed `design-context-template.md` to `dcf-prompt.md`.
4. **Continue the normal artifact flow** — Generate, validate, freeze as usual.

For greenfield SADs, no intake form is needed — the SAD is generated directly from the frozen PRD and frozen ACF.

### Codebase Analysis (Brownfield Prerequisite)

Before generating ACF, DCF, or SAD for a project with an existing codebase, run the codebase analysis prompt (`codebase-analysis-prompt.md`) to produce a structured report of what exists:

- Technology stack, frameworks, and libraries
- Architectural patterns and component boundaries
- External integrations and data patterns
- Testing infrastructure and code conventions
- Security patterns and deployment configuration

The codebase analysis report is **input material**, not a governed artifact. It is not frozen, validated, or promoted.

### Brownfield Intake Sequence

The codebase analysis prompt produces four outputs. Three of them map directly to intake form templates:

| Codebase Analysis Output | Fills This Intake Form | Feeds This Artifact |
|--------------------------|----------------------|-------------------|
| **Output A**: Architecture Context | `architecture-context-template.md` | ACF |
| **Output B**: Design Context | `design-context-template.md` | DCF |
| **Output C**: System Context | `system-context-template.md` | SAD |
| **Output D**: Ambiguities and Gaps | No template — human review only | All |

**Step-by-step:**

1. **Run codebase analysis** — Provide the codebase to `codebase-analysis-prompt.md`. It produces Outputs A, B, C, and D.
2. **Review Output D first** — Ambiguities and gaps require human judgment. Resolve what you can before proceeding.
3. **Review and edit each pre-filled intake form** — The AI extracted facts from the codebase, but it may have missed context, misidentified patterns, or left fields blank. The human corrects, adds organizational context (e.g., PRD constraints, compliance requirements, team standards), and confirms accuracy.
4. **Use the reviewed intake forms as inputs** — Feed the corrected `architecture-context-template.md` to `acf-prompt.md`, the corrected `design-context-template.md` to `dcf-prompt.md`, and the corrected `system-context-template.md` to `sad-prompt.md`.
5. **Continue the normal artifact flow** — Generate, validate, freeze as usual.

**Important:** Pre-filled forms are a starting point, not a finished product. The human must review and edit before using them as generation input. Fields the AI could not determine from the codebase are left blank — the human fills these from organizational knowledge, PRD constraints, or team standards.

**Infrastructure and platform context:** The codebase analysis extracts infrastructure details from IaC files (Terraform, CloudFormation, Pulumi), Kubernetes manifests, CI/CD pipelines, and monitoring configurations when present in the repository. However, much infrastructure context lives outside the codebase — network topology, environment differences, managed service configurations, scaling rules, disaster recovery procedures, and cloud console settings. During the human review step (Step 3 above), supplement the pre-filled infrastructure sections from operational knowledge, cloud provider consoles, network diagrams, runbooks, and team expertise. The Architecture Context and System Context intake forms include dedicated infrastructure sections for this purpose.

### How Brownfield Changes the Flow

| Step | Greenfield | Brownfield |
|------|-----------|------------|
| Intake Facilitation | Run `greenfield-intake-prompt.md` — extracts PRD constraints, lists team decisions | Run `codebase-analysis-prompt.md` — extracts facts from codebase |
| ACF Intake Form | AI extracts PRD constraints; human decides runtime, deps, infra | AI pre-fills from codebase analysis; human reviews and supplements |
| DCF Intake Form | AI extracts PRD constraints; human decides standards and conventions | AI pre-fills from codebase analysis; human reviews and supplements |
| SAD Intake Form | Not used — SAD generates directly from PRD + ACF | Human reviews AI-pre-filled system context form |
| TDD onward | No difference | No difference |

The artifact flow itself does not change. The codebase analysis simply provides the factual foundation that would otherwise require manual extraction by the human.

---

## Step 1: PRD (Product Requirements Document)

**What:** Fill the PRD slot — either by receiving a Frozen Discovery PRD from the Product Intelligence Kit (Path A), or by generating one from a Product Brief (Path B). Both paths produce a frozen PRD. See the **PRD Entry Paths** section above for the full decision flow.

**Output:** Frozen PRD

### Path A — Discovery PRD (from Product Intelligence Kit)

**Inputs:** Frozen Discovery PRD (DPRD)
**Prompt:** None — no generation required
**Validator:** `prd-validator.md` (acceptance check only)

1. Receive frozen DPRD from Product Intelligence Kit
2. Place DPRD as `docs/sdlc/01-prd.md`
3. Run `prd-validator.md` against the DPRD (acceptance check — not generation)
4. Save result as `docs/sdlc/01-prd-validation.json`
5. If PASS: freeze PRD slot
6. If FAIL: return failing validator output to PIK team — do not begin ACF

### Path B — Direct Entry (Product Brief)

**Inputs:** Product Brief (human-written intake)
**Prompt:** `prd-prompt.md`
**Validator:** `prd-validator.md`
**Gate:** Validator PASS + human approval

1. Human completes a Product Brief (goals, constraints, context)
2. Generate PRD using `prd-prompt.md` with the Product Brief as input
3. Run `prd-validator.md` against the generated PRD
4. Fix blocking issues only; re-run validator until PASS
5. Human reviews and approves
6. Freeze PRD

---

## Step 2: ACF (Architecture Context File)

**What:** Define architectural guardrails that constrain all downstream design.

**Prompt:** `acf-prompt.md`
**Inputs:** Architecture Context intake form (`architecture-context-template.md`), organizational standards, platform constraints, security and compliance requirements. For greenfield projects, use `greenfield-intake-prompt.md` to facilitate form completion. For brownfield projects, use `codebase-analysis-prompt.md` Output A to pre-fill the intake form.
**Validator:** `acf-validator.md`
**Gate:** Validator PASS + human approval
**Output:** Frozen ACF

### Steps
1. Generate ACF using `acf-prompt.md` with organizational context as input
2. Run `acf-validator.md` against the generated ACF
3. Fix blocking issues only; re-run validator until PASS
4. Human reviews and approves
5. Freeze ACF

If an organization-wide ACF already exists and is frozen, skip this step and use the existing ACF.

---

## Step 3: SAD (System Architecture Design)

**What:** Define the system shape — boundaries, components, and architectural decisions.

**Prompt:** `sad-prompt.md`
**Inputs:** Frozen PRD + Frozen ACF. For brownfield projects, also provide a System Context intake form (`system-context-template.md`); use `codebase-analysis-prompt.md` Output C to pre-fill it.
**Validator:** `sad-validator.md`
**Gate:** Validator PASS + human approval
**Output:** Frozen SAD

The SAD prompt includes **intent verification**: the AI restates upstream intent in Section 1 before generating. If scope cannot be reconciled, the AI stops and flags the conflict.

### Steps
1. Generate SAD using `sad-prompt.md` with frozen PRD and frozen ACF as inputs
2. Run `sad-validator.md` against the generated SAD
3. Fix blocking issues only; re-run validator until PASS
4. Human reviews and approves
5. Freeze SAD

---

## Step 4: DCF (Design Context File)

**What:** Define design standards and quality expectations that constrain TDD creation.

**Prompt:** `dcf-prompt.md`
**Inputs:** Design Context intake form (`design-context-template.md`), organizational design standards, testing expectations, operational expectations. For greenfield projects, use `greenfield-intake-prompt.md` to facilitate form completion. For brownfield projects, use `codebase-analysis-prompt.md` Output B to pre-fill the intake form.
**Validator:** `dcf-validator.md`
**Gate:** Validator PASS + human approval
**Output:** Frozen DCF

### Steps
1. Generate DCF using `dcf-prompt.md` with organizational standards as input
2. Run `dcf-validator.md` against the generated DCF
3. Fix blocking issues only; re-run validator until PASS
4. Human reviews and approves
5. Freeze DCF

If an organization-wide DCF already exists and is frozen, skip this step and use the existing DCF.

---

## Step 5: TDD (Technical Design Document)

**What:** Define a buildable technical design — interfaces, contracts, deployment, failure handling, testing.

**Prompt:** `tdd-prompt.md`
**Inputs:** Frozen SAD + Frozen DCF
**Validator:** `tdd-validator.md`
**Gate:** Validator PASS + human approval
**Output:** Frozen TDD

The TDD prompt includes **intent verification**: the AI restates upstream intent in Section 1 before generating.

TDD §4 (Interfaces and Contracts) defines the contracts that enable parallel development. Cross-boundary interfaces must be **stub-ready** — concrete enough that a developer implementing either side can write a working stub of the other side from the contract alone.

### Steps
1. Generate TDD using `tdd-prompt.md` with frozen SAD and frozen DCF as inputs
2. Run `tdd-validator.md` against the generated TDD
3. Fix blocking issues only; re-run validator until PASS
4. Human reviews and approves — verify that cross-boundary interfaces are stub-ready for parallel development
5. Freeze TDD

---

## Step 6: WDD (Work Design Document)

**What:** Decompose a frozen TDD into atomic, executable work items grouped into business-testable work groups.

**Prompt:** `wdd-prompt.md`
**Inputs:** Frozen TDD
**Validator:** `wdd-validator.md` (document-level), then `dor-validator.md` (per work item)
**Gate:** Both validators PASS + human approval
**Output:** Frozen WDD with all work items passing DoR

The WDD prompt includes **intent verification**: the AI restates upstream intent in Section 1 before generating. Each work item also includes an **Interface Contract Reference** identifying which TDD §4 contract it implements (provider) or consumes (consumer), enabling parallel development coordination.

### Steps
1. Generate WDD using `wdd-prompt.md` with frozen TDD as input
2. Run `wdd-validator.md` against the full WDD document
3. Fix blocking issues only; re-run validator until PASS
4. Run `dor-validator.md` against each work item individually
5. Fix blocking issues only; re-run validator until PASS (if fixes require WDD changes, re-run `wdd-validator.md` as well)
6. Run consistency check (see Step 6a)
7. Human reviews and approves
8. Freeze WDD
9. Optionally, run `team-composition-prompt.md` with the frozen WDD (and frozen TDD) to produce a team composition document identifying roles, required skills, deliverables, and parallel development opportunities. This is advisory — it informs staffing decisions but does not gate execution.
10. Optionally, run `wdd-csv-export-prompt.md` with the frozen WDD to export work items as CSV for import into a work management tool (Jira, Azure DevOps, Linear, etc.). The prompt supports custom field mappings per tool. This is advisory — the human reviews the CSV before importing.
11. Begin execution

---

## Step 6a: Consistency Check

**What:** Verify that intent, requirements, constraints, and scope flow consistently across all frozen artifacts without gaps, contradictions, or unauthorized expansion.

**Prompt:** `consistency-prompt.md`
**Inputs:** All frozen upstream artifacts (PRD, ACF, SAD, DCF, TDD) and the validated WDD (not yet frozen). Frozen addendums are included where they exist.
**Validator:** `consistency-validator.md`
**Gate:** Validator PASS (fixing inconsistencies may require the Re-entry Protocol for upstream frozen artifacts, followed by downstream cascade revalidation)
**Output:** Consistency report (JSON) confirming cross-artifact traceability

The consistency check is distinct from per-artifact validators. Per-artifact validators check internal quality (is this PRD well-formed?). The consistency check verifies **cross-artifact relationships** (does every PRD requirement trace through to a WDD work item?).

### Checks Performed

1. **Requirement Coverage** — Every PRD requirement maps to at least one SAD component
2. **Requirement-to-Work Traceability** — Every PRD functional requirement traces to at least one WDD work item
3. **Scope Containment** — No downstream artifact introduces scope absent from upstream
4. **Non-Goal Enforcement** — No downstream artifact contradicts a stated non-goal
5. **Constraint Propagation** — No downstream artifact violates an ACF constraint
6. **Interface Alignment** — SAD integration points match TDD specifications
7. **Addendum Integration** — Frozen addendum changes are reflected downstream
8. **Boundary Consistency** — SAD boundaries, data ownership, and failure modes align with TDD specifications

### Steps
1. Gather all frozen upstream artifacts, their frozen addendums, and the validated WDD
2. Run `consistency-prompt.md` with all artifacts as input
3. Review the consistency report — examine any inconsistencies flagged
4. Run `consistency-validator.md` against the report
5. Fix blocking inconsistencies in the appropriate upstream artifact (using the Re-entry Protocol if the artifact is already frozen)
6. Re-run consistency check until PASS

### When to Run

- **Required:** After DoR validation passes and before WDD freeze
- **Optional:** After any addendum is frozen, to verify downstream coherence
- **Optional:** At any point during the artifact flow as a progress check (the check handles missing artifacts gracefully, issuing warnings rather than failures)

---

## Step 7: Execution Plan

**What:** Produce an ordered execution plan that sequences every work item through the four execution phases, respecting work group order and dependencies.

**Prompt:** `execution-plan-prompt.md`
**Inputs:** Frozen WDD + Frozen TDD + Frozen ACF + Frozen DCF
**Gate:** Human approves execution plan
**Output:** Execution order and per-work-item context (relevant TDD/ACF/DCF sections extracted per item)

### Steps
1. Run `execution-plan-prompt.md` with the frozen WDD, TDD, ACF, and DCF
2. Review the execution order — confirm dependency sequencing and work group order
3. Approve the plan
4. For each phase, use the corresponding phase assembly prompt to generate ready-to-use prompts per work item:
   - `execution-plan-tests-prompt.md` — assembles Phase 1 (Tests) prompts from the execution plan
   - `execution-plan-plan-prompt.md` — assembles Phase 2 (Plan) prompts, includes approved Phase 1 output
   - `execution-plan-code-prompt.md` — assembles Phase 3 (Code) prompts, includes approved Phase 1 and Phase 2 output
   - `execution-plan-review-prompt.md` — assembles Phase 4 (Review) prompts, includes Phase 3 diff and test results
5. Run each assembled prompt in a separate AI session, approve the output, then generate the next phase's prompts

---

## Step 8: Execute (Per Work Item)

Once the execution plan is approved, execute each work item in plan order. See **Part 2: The Execution Loop** below.

---

## Step 9: Work Groups

When all items in a work group are complete, the group's business-level acceptance criteria can be verified. See **Work Groups and Business Acceptance Testing** below.

---

## Step 10: ORD (Operational Readiness Document)

**What:** Verify that all operational requirements from TDD, ACF, and DCF have been implemented and are working.

**Prompt:** `ord-prompt.md`
**Inputs:** Frozen TDD (§5-7, §9) + Frozen ACF (§3, §5-6) + Frozen DCF (§5)
**Validator:** `ord-validator.md`
**Gate:** Validator PASS + human approval
**Output:** Approved ORD — system is production ready

The ORD operates at the **system level**, not the work item level. Per-item evidence (test results, review outcomes) is captured during execution. The ORD gathers system-level evidence (deployment, observability, alerting, failure handling) from a **deployed system**. Deploy according to TDD §5, then gather evidence from the running system to populate the ORD.

### Steps
1. Deploy according to TDD §5 (Build and Deployment Approach)
2. Generate ORD using `ord-prompt.md` with frozen TDD, ACF, and DCF as inputs
3. Gather concrete evidence from the deployed system for each verification item (evidence must be concrete, timestamped, traceable, and retrievable)
4. Run `ord-validator.md` against the completed ORD
5. Fix blocking issues only; re-run validator until PASS
6. Human reviews and approves
7. System is production ready

---

# Part 2: The Execution Loop

Once the WDD passes the DoR Validator, the consistency check, and human approval, and is frozen, its work items enter the execution loop:

**Tests → Plan → Code → Review → Done**

Each phase has a prompt that drives AI behavior and a gate that controls progression. Skipping phases weakens safety.

**Execution order:** If a work item's inputs depend on another item's outputs, the dependency must be completed first. Execute items in dependency order. Independent items within the same work group may be executed in parallel if they share no file-level conflicts and the executor can maintain separate context for each. In practice, parallel execution is most valuable when different people (or separate AI sessions) work on different items that touch different subsystems. When items modify overlapping files, sequential execution avoids merge conflicts and context confusion. The execution plan marks items as "parallel-safe" or "sequential" based on file overlap analysis.

**Parallel development with interface contracts:** When two work items reference the same TDD §4 contract — one as **provider** and one as **consumer** — they can be developed in parallel. Each developer builds a stub of the other side from the TDD contract, implements their side independently, then integrates when both are ready. The WDD's Interface Contract References identify these provider/consumer relationships explicitly. See `how-to-adapt.md` → "Parallel Development with Interface Contracts" for the full pattern.

**Cancellation:** A human may cancel a work item at any point with a recorded reason. If other items depend on the cancelled item, assess and resolve the impact before continuing. Cancellation does not require re-entry unless the WDD itself needs to change.

**Scope discovery during execution:** If implementation reveals that a work item's approach is more complex than anticipated but its scope is correct, handle it in the Plan phase — no WDD change needed. If implementation reveals that new work items are needed, existing items need different interfaces, or scope was misunderstood, trigger the Re-entry Protocol to update the WDD.

### Execution Principles

1. **Tests define targets** — If tests aren't defined, we're not ready to implement
2. **Plan before code** — Approve an approach before changing files
3. **Small, reversible steps** — Keep diffs focused; iterate with tests
4. **Verification is built into review** — Not a separate step; the review prompt checks completeness
5. **Auditability** — Track what was done, what was tested, and what was reviewed

### Execution Artifact Naming

Phase outputs are saved as files alongside the execution plan. Use this naming convention:

- `{nn}-{wdd-item-id}-context.md` — Per-work-item context extracted from upstream artifacts
- `{nn}-{wdd-item-id}-tests.md` — Phase 1 approved test specifications
- `{nn}-{wdd-item-id}-plan.md` — Phase 2 approved implementation plan
- `{nn}-{wdd-item-id}-review.md` — Phase 4 review output

Where `{nn}` is a sequence number continuing from the execution plan file (e.g., if the execution plan is `07-execution-plan.md`, the first item's context would be `08-WDD-PROJ-001-context.md`).

Phase 3 (Code) does not produce a separate document — its output is committed code and test results.

### Execution Inputs

Before entering the loop, assemble everything needed for the work item:

- WDD work item (intent, scope, inputs, outputs, acceptance criteria, DoD)
- TDD interface contracts and relevant design sections
- ACF security and compliance guardrails
- DCF testing and operational expectations
- Relevant source code and configuration

**Rules:**
- Curate context deliberately — do not dump entire codebases
- Include constraints, conventions, and patterns that apply
- Include logs, traces, or error messages if fixing a bug
- Remove or replace sensitive data with placeholders
- If requirements are unclear, clarify before proceeding — do not assume

---

### Phase 1: Tests

**Goal:** Turn acceptance criteria into verifiable test specifications before writing code.

**Prompt:** `test-prompt.md`
**Gate:** Human approves test specifications

**Inputs:**
- WDD acceptance criteria (Given/When/Then format)
- TDD testing strategy (test layers, pass/fail criteria)
- DCF testing expectations (required test types, evidence requirements)

**Output:** Approved test specifications grouped by category:
1. **Acceptance tests** — Direct mapping from WDD Given/When/Then criteria
2. **Failure tests** — At least one per acceptance criterion
3. **Edge case tests** — Boundary conditions, empty inputs, invalid states
4. **Regression tests** — If fixing a bug, a test that fails before the fix and passes after

**Test Structure Specification:**
In addition to test specifications, Phase 1 output must include:
- **Test file path** — Where the test file will be created (following project conventions)
- **Test grouping structure** — Logical groupings and individual test names
- **Mapping** — Which test group maps to which acceptance criterion

This structure ensures Phase 3 implements tests in the correct location with the correct organization. The test structure is part of the approved output — Phase 3 must follow it exactly.

---

### Phase 2: Plan

**Goal:** Choose the smallest change that makes tests pass.

**Prompt:** `plan-prompt.md`
**Gate:** Human approves implementation plan

**Inputs:**
- Approved test specifications
- TDD interface contracts (method signatures, return types, status codes)
- Relevant source code

**Output:** An approved implementation plan identifying files to change, interfaces to lock down, dependencies, risks, and sequencing.

**Existing Tests to Update:** If this work item modifies a shared interface (adds methods, changes return types, extends data structures), the plan must list existing test files whose mocks or assertions will break due to the change. For each file, state the specific update needed (e.g., "add stub methods to mock factory", "update expected shape in test AT-03-06"). This prevents discovery of mock drift during Phase 3. Run `mock-impact-prompt.md` with the WDD work item and TDD §4 interface contracts to identify affected files before writing the plan.

---

### Phase 3: Code

**Goal:** Implement the plan. Make tests pass.

**Prompt:** `code-prompt.md`
**Gate:** All tests pass

Test specifications from Phase 1 are implemented as executable test code in this phase. Write the test code first, confirm it fails, then write the production code to make it pass.

**Rules:**
- Follow the approved plan
- Write test code from approved test specifications before writing production code
- Make only the minimal changes needed to pass approved tests
- Touch only files identified in the plan
- Keep diffs small and focused — one logical change per step
- If a dependency doesn't exist or version differs, ask before adding
- If anything is unclear, ask before coding
- Do NOT expand scope beyond the WDD work item

**Iteration:**
- Run tests after each change
- If tests fail, map each failure to its acceptance criterion before attempting a fix (structured failure feedback)
- If the same failure persists after 3 fix attempts, stop and escalate to the human with a structured report (what failed, what was tried, why it didn't work)
- Do not loop indefinitely — if progress stalls (same failure repeating, or new failures replacing old ones without net progress), escalate rather than retry
- If answers degrade or context is lost, restart with fresh context
- Multiple iterations are normal

**Completion Verification:**
Before signaling Phase 3 complete, verify:
1. All acceptance criterion tests pass
2. All failure condition tests pass
3. No regressions in existing tests
4. Every file changed is in the approved plan's file list
5. No scope expansion detected (no capabilities beyond what tests require)

If any verification item fails, do not proceed to Phase 4 — fix or escalate.

**Context Recovery (when iteration requires a fresh session):**
When Phase 3 requires multiple iterations and the AI session must be restarted (context window exhaustion, degraded answers, or session timeout):
1. Start the new session with the approved test specs, approved plan, and the work item context file
2. Include the current state of changed files (not the original versions)
3. Include the most recent test output (full error messages and stack traces)
4. Summarize what has been attempted: which approaches worked, which failed, and why
5. Do not re-explain the entire project — the context file contains everything needed

If the same test fails after three focused debugging attempts, escalate to the Execution Refinement Ladder before continuing.

---

### Phase 4: Review

**Goal:** Confirm the change is safe, correct, within scope, verified, and ready to merge.

**Prompt:** `review-prompt.md`
**Gate:** Human approves PR

**AI Review (First Pass):**
The review prompt checks both correctness and completeness:
- Scope adherence and interface compliance
- Test coverage (acceptance, failure, edge cases)
- Code standards (DCF design principles and quality bars)
- Verification (all tests passing, DoD satisfied, evidence present)
- Security concerns (ACF guardrails) and factual risks

**Human Review (Required):**
- AI review is advisory — human review is mandatory
- Review the PR with an explainer covering:
  - What changed and why (link to WDD item)
  - Tests added/updated and what they assert
  - Test results (pass/fail evidence)
  - Risks or known limitations
- Changes that expand scope beyond the WDD work item must be rejected

---

### Human-Assigned Work Items

WDD items with Assignee Type "Human" follow a **verification checklist model** instead of the four-phase AI execution loop. These items typically cover packaging, distribution, deployment verification, infrastructure provisioning, or other tasks that require direct human action.

**Execution model for human-assigned items:**
1. Human performs the work described in the WDD item
2. Human completes each acceptance criterion as a verification checklist (check-and-record, not Given/When/Then)
3. Human records evidence for each checklist item (screenshots, logs, command output)
4. Another human reviews the evidence against the WDD item's acceptance criteria and DoD

Human-assigned items still follow the execution plan order and respect dependency chains. They still require evidence and must satisfy their Definition of Done. The difference is that they do not pass through the AI-driven Tests/Plan/Code/Review phases.

---

### Execution Refinement Ladder

**Trigger:** The Review phase returns CONCERNS or BLOCKED, or tests fail repeatedly during Code.

When this happens, work through these steps top-down before re-entering the failing phase. Multiple iterations are normal.

1. **Clarify** — What assumptions were made? What details are missing? Resolve ambiguity before continuing.
2. **Simplify** — Reduce complexity. Fewer branches, less nesting, clearer flow. Keep behavior identical.
3. **Refactor** — Align with team conventions and patterns. Ensure idiomatic, maintainable code without unnecessary abstractions.
4. **Harden** — Add input validation, meaningful error messages, and error handling aligned with TDD failure handling patterns.
5. **Explain** — Add documentation where the logic isn't self-evident. Summarize design tradeoffs and risks.

---

### Completion Criteria

A WDD work item is complete when:

1. All acceptance criteria tests pass
2. All DoD checklist items are satisfied
3. PR is reviewed and approved (AI + human)
4. Evidence is generated and accessible
5. No scope expansion occurred beyond the WDD work item

---

### Version Control Workflow

This playbook does not prescribe a specific branching strategy. However, the following practices align with the execution loop and must be adapted to the project's version control conventions:

**Branch per work item:** Create a branch for each WDD work item before entering Phase 3. The branch name should include the WDD Item ID for traceability.

**Commit discipline:**
- Commit after Phase 3 produces passing tests (test code + production code)
- Each commit should represent one logical change within the work item
- Commit messages should reference the WDD Item ID

**Pull request per work item:** Phase 4 (Review) assumes a reviewable pull request exists. Create the PR after all tests pass in Phase 3. The PR description should link to the WDD work item and include test evidence.

**Merge after review:** Merge only after both AI review (Phase 4) and human review approve. The WDD item's Definition of Done includes "PR merged."

**Work group gates:** Do not begin the next work group until all PRs from the current group are merged and the work group gate is satisfied.

---

### Work Groups and Business Acceptance Testing

Work groups organize related WDD items into **business-testable capabilities**. They enable incremental business acceptance testing (BAT) before the entire TDD scope is complete.

#### How Work Groups Work

- Work groups are defined during WDD generation and frozen with the WDD
- Each group names its member items and states what business capability becomes testable when all items are complete
- Groups are organizational labels — they do not change item scope, granularity, or execution order
- Every work item belongs to exactly one group

#### Work Group Gates

When all items in a work group are complete (all PRs merged, all tests passing), record a work group gate before proceeding to the next group.

**Gate artifact file:** Persist the gate as `{nn}-wg-{n}-gate.md` in the execution artifacts directory, using the next available sequence number. The gate file is written after all items in the group pass review.

```
## WG-{n} Gate: {group name}
- Tests: {count} passing, 0 failing
- Test count delta: +{n} from previous gate (or from baseline for WG-1)
- Build: clean (zero errors)
- Items completed: {list of WDD Item IDs}
- Reviews: all PASS
- Evidence: {link or path to test results}
```

A work group gate is a checkpoint, not a validator. It records cumulative state. If any line cannot be satisfied, the group is not complete.

Run `work-group-gate-prompt.md` with all member item review files and test results to verify conditions and produce the gate artifact. The prompt outputs the ready-to-save gate file, or a BLOCKED report listing which conditions are not met.

#### Business Acceptance Testing (BAT)

BAT verifies that a work group's business capability works end-to-end. It is conducted after the work group gate is recorded and before proceeding to the next group or to ORD.

**Who runs it:** A product owner, stakeholder, or QA lead — someone who can verify business outcomes without knowledge of implementation internals.

**Prompt:** `bat-prompt.md`
**Inputs:** WDD work group definition (group ID, business capability, member item IDs, group-level acceptance criteria) + individual WDD items for all member items
**Output:** Document 1 — a structured manual verification protocol (one step per group-level acceptance criterion). Document 2 — a results recording template that becomes the evidence artifact.

**Steps:**
1. Confirm the work group gate for WG-{n} is recorded
2. Run `bat-prompt.md` to generate the verification protocol
3. A product owner or stakeholder executes the protocol against the deployed system
4. Record results in the Document 2 template
5. File the completed results as `{nn}-wg-{n}-bat-results.md`

BAT verifies:
1. Group-level acceptance criteria are satisfied (business capability works end-to-end)
2. Results are recorded as evidence

If BAT fails, the work group is not complete. Use the following escalation paths:

**Path A — Fixable within work group scope:**
The failing criterion identifies a work item gap (missing implementation, incorrect behavior, integration defect). The capability is in scope and the fix is bounded.
1. Identify which WDD work item(s) are responsible for the gap
2. Fix the failing items (apply the execution loop — tests, plan, code, review)
3. Re-record the work group gate with the updated item list
4. Re-run BAT against the fixed system
5. File the new results as a revised `{nn}-wg-{n}-bat-results.md`

**Path B — Scope misunderstanding requiring a product decision:**
The failing criterion reveals that the work group's scope was insufficient or incorrectly defined — the capability cannot be satisfied by fixing existing items within current scope.
1. Stop. Do not attempt to fix items beyond their defined scope.
2. Create a **BAT Escalation Record** (one page) capturing:
   - Which acceptance criterion failed
   - What the gap is (what the system does vs. what was expected)
   - Whether the gap is a scope gap (missing work items) or a design gap (wrong approach)
   - Resolution options with trade-offs
3. Bring the record to the product owner for a scope decision
4. If new work items are required: add them to the WDD (re-entry protocol applies), re-validate WDD, re-run execution loop for new items, then re-run BAT
5. If the acceptance criterion itself was wrong: update the WDD acceptance criteria (re-entry), re-validate, re-run BAT

**BAT Failure is a Gate**
A work group with a failing BAT is not complete. Do not proceed to the next work group or to ORD until BAT passes or the scope decision explicitly defers the failing criterion to a follow-on engagement (documented in the escalation record).

---

# Part 3: Rules and Reference

## Artifact Responsibilities (Single-Responsibility Rule)

Each artifact answers exactly one question:

- **PRD** — What problem are we solving and why?
- **ACF** — What architectural guardrails apply?
- **SAD** — What is the system shape?
- **DCF** — What design standards constrain technical design?
- **TDD** — How will this be built and operated?
- **WDD** — What is the smallest executable work?
- **WDD Item + DoR** — Is this ready to execute now?

Execution prompts each answer one question per work item:

- **Test specs** (`test-prompt.md`) — What must pass before this is done?
- **Plan** (`plan-prompt.md`) — What is the smallest change that makes tests pass?
- **Review** (`review-prompt.md`) — Is this safe, correct, verified, and within scope?

Post-execution:

- **ORD** — Is this ready to run in production?

Quality and governance prompts each answer one question:

- **Consistency Check** (`consistency-prompt.md`) — Do all artifacts trace consistently from upstream to downstream?
- **Impact Analysis** (`impact-analysis-prompt.md`) — What downstream artifacts and work items are affected by this change?

Utility prompts produce input material (not governed artifacts):

- **Greenfield Intake** (`greenfield-intake-prompt.md`) — What decisions must a greenfield team make before generating ACF and DCF? (extracts PRD constraints, lists required decisions)
- **Codebase Analysis** (`codebase-analysis-prompt.md`) — What exists in this codebase? (feeds ACF, DCF, SAD generation for brownfield projects)
- **Mock Impact** (`mock-impact-prompt.md`) — Which existing test files will break when this work item changes a shared interface? (feeds Phase 2 plan)
- **Work Group Gate** (`work-group-gate-prompt.md`) — Are all gate conditions met for this work group? (produces the gate artifact file)
- **Business Acceptance Testing** (`bat-prompt.md`) — Does this work group's business capability work end-to-end? (produces verification protocol and results template)

Artifacts and prompts that answer more than one question are invalid.

---

## Artifact Promotion Model

Artifacts are **promoted**, not rewritten.

- Each downstream artifact:
  - Narrows scope
  - Increases specificity
  - Must not reinterpret upstream intent
- Promotion occurs only after validator PASS
- Validator failure returns the artifact to its own stage

Downstream artifacts may **not** expand scope.

---

## Freeze Rules

Freezing an artifact locks its intent and scope.

- **PRD Freeze** — Product intent locked
- **ACF Freeze** — Architectural guardrails locked
- **SAD Freeze** — Architecture locked
- **DCF Freeze** — Design standards locked
- **TDD Freeze** — Technical design locked
- **WDD Freeze** — Work items locked; execution may begin

Breaking a freeze requires explicit re-entry to the prior stage (see Re-entry Protocol below).

> **Freeze semantics:** A freeze is a human commitment, not a technical lock. Nothing prevents editing a Markdown file after it is frozen. The enforcement mechanism is the Git commit that immediately follows the freeze declaration — that commit is the audit trail. **Frozen artifacts must be committed immediately on approval.** The commit message should identify the artifact and its status (e.g., `docs: freeze 01-prd.md`). If a frozen artifact is edited without following the re-entry protocol, the Git history exposes the deviation.

---

## Re-entry Protocol

When a frozen artifact must change, follow this protocol:

### Before Re-entry: Impact Analysis (Recommended)

Before committing to re-entry, run the impact analysis prompt (`impact-analysis-prompt.md`) to map downstream effects. Provide the proposed change description, the target artifact type, and all frozen artifacts.

This step is optional but strongly recommended — it helps assess cascade cost before committing to the change. The impact report identifies which downstream artifacts would need revision, which work items are affected, and whether the Re-entry Protocol is necessary.

Impact analysis is also useful before proposing an addendum to any frozen artifact, even when re-entry may not be needed. For example, an addendum to the WDD may not require upstream re-entry, but it can still affect in-progress or completed work items. Running impact analysis first helps assess the full cost of the change.

### When Re-entry Is Required
- A frozen artifact contains an error that affects downstream work
- Requirements change after freeze (business priority shift, new constraint)
- A downstream validator repeatedly fails due to upstream ambiguity

### Re-entry Rules
1. **Identify the highest affected artifact** — Change at the earliest point in the flow, not downstream
2. **Unfreeze only that artifact** — Do not unfreeze downstream artifacts preemptively
3. **Make the change** — Edit the artifact to resolve the issue
4. **Re-validate** — Run the artifact's validator; it must PASS before re-freezing
5. **Re-freeze** — Lock the artifact again
6. **Cascade validation** — Re-validate all downstream frozen artifacts against the updated upstream; fix any that now fail
7. **Assess execution impact** — If re-entry affects a WDD with items in progress or completed, identify which work items are impacted by the change. If impact analysis was run before re-entry, reference its report for work item impact and severity classifications. Impacted completed items must be re-verified against the updated upstream. Impacted in-progress items must incorporate the change. Unaffected items continue as-is.

### What Re-entry Does NOT Allow
- Expanding scope beyond the original intent
- Skipping validators on the changed artifact
- Changing downstream artifacts without re-validating them

### Responsibility
- **Humans** decide whether re-entry is warranted
- **AI** may flag the need for re-entry but must not unfreeze artifacts autonomously

---

## Cross-Kit Re-Entry Protocol (Upstream PRD Change)

When the PRD was delivered from the Product Intelligence Kit (Path A) and that DPRD must change after EEK artifacts have been created:

### When This Applies
- The Product Intelligence Kit team notifies this kit that a frozen DPRD must be revised
- A change to the DPRD affects §2 Problem Statement, §3 Goals, §4 Non-Goals, §6 Requirements, or §11 Acceptance Criteria

### Protocol

1. **Receive notification** — The PIK team sends a proposed change description (what section is changing, what is changing, why) before re-validating the DPRD. Work on artifacts and work items that depend on the affected sections pauses. Work on sections unaffected by the proposed change may continue.

2. **Run impact analysis on the proposal** — Run `impact-analysis-prompt.md` using the proposed change description (not the final updated DPRD) and all frozen EEK artifacts. This assesses downstream cost before the PIK commits to the change. Report findings back to the PIK team — which artifacts are affected, which work items are impacted, at what severity.

3. **Joint decision to proceed** — Both team leads review the impact report and agree to proceed. If impact is contested or too large, this is the decision point to pause or reframe before the PIK invests in re-entry.

4. **Receive updated DPRD** — The PIK team delivers the updated, re-validated, re-frozen DPRD.

5. **PRD acceptance check** — Re-run `prd-validator.md` against the updated DPRD. All 6 PRD gates must PASS.

6. **Re-freeze PRD slot** — Replace `docs/sdlc/01-prd.md` with the updated DPRD and record as version 2 (or append version note).

7. **Cascade re-validation** — Re-validate all affected EEK artifacts (SAD, TDD, WDD) top-down against the updated PRD. Follow the standard Re-entry Protocol for cascade rules.

8. **Human approval** — Required at each step. Neither kit self-approves cross-kit changes.

### What Triggers This vs. Standard Re-entry

| Change | Protocol |
|--------|----------|
| PRD §2 Problem Statement change | Cross-kit re-entry — notify PIK team, full cascade |
| PRD §3 Goals change | Cross-kit re-entry — notify PIK team, full cascade |
| PRD §4 Non-Goals change | Cross-kit re-entry — notify PIK team, full cascade |
| PRD §6 Requirements change | Cross-kit re-entry — notify PIK team, full cascade |
| PRD §11 Acceptance Criteria change | Cross-kit re-entry — notify PIK team, full cascade |
| PRD §8 Assumptions change | EEK notification to PIK; run impact analysis; cascade likely not needed |
| PRD §10 Open Questions resolved | EEK notification to PIK; assess impact; cascade if resolution changes scope |
| Typo / clarification only | PIK delivers updated DPRD; re-run acceptance check; no cascade |

**Determining "typo / clarification only":** Both the Product team lead and Engineering team lead must agree that the change does not alter meaning, intent, or scope. If either lead disagrees, treat as the next higher category (Assumptions or full cross-kit re-entry, depending on what changed). When in doubt, escalate.

### In-Progress Work Items During Cross-Kit Re-Entry

Cross-kit re-entry follows the same execution impact rules as standard re-entry (see Re-entry Protocol §"Assess execution impact" above). Specifically:

- **Unaffected work items**: continue execution without interruption
- **In-progress items affected by the change**: pause, incorporate the upstream change after cascade re-validation, then resume
- **Completed items affected by the change**: re-verify against the updated upstream; if they no longer satisfy acceptance criteria, re-open

The impact analysis result (step 2 of the protocol above) identifies which work items are affected and at what severity.

### If DPRD Fails PRD Acceptance Check

If the DPRD fails the PRD acceptance check (`prd-validator.md`), the fix belongs in the DPRD — not in this kit. Return the failing validator output to the PIK team and request a corrected DPRD. Do not modify the DPRD directly in this kit. Work on downstream artifacts does not begin until a passing acceptance check has been recorded.

---

## Intent Verification (Built into Generation)

Before generating **SAD**, **TDD**, or **WDD**, the AI must verify its understanding of upstream intent.

### How It Works
- Each generation prompt instructs the AI to restate the upstream intent, constraints, and non-goals in Section 1 of the artifact
- If the AI cannot reconcile scope with upstream artifacts, it must stop and flag the conflict instead of generating
- The downstream validator enforces intent integrity as a hard gate

### Why This Works
- No separate manual step — verification is embedded in generation
- Misalignment is caught by the validator (`intent_integrity`, `intent_alignment`, `traceability`)
- Scope expansion is blocked by both the prompt rules and the validator

---

## Granularity Rules (WDD Work Items)

Executable work must satisfy all of the following:

- One observable outcome
- One repository or subsystem
- One pull request
- One environment
- No design decisions
- Bounded execution time

Work that violates these rules must be split.

---

## Splitting Guidance (WDD Work Items)

When a work item violates granularity rules, split it using these patterns:

### Cross-Repo Outcome
A single outcome requires changes in multiple repositories (e.g., database migration + application code).

**Split into:** One item per repository, each with its own observable outcome.
- Item 1: Apply database migration (repo: infra, outcome: schema updated)
- Item 2: Update application to use new schema (repo: app, outcome: feature works with new schema, dependency: Item 1)

### Sequential Dependency
An outcome requires steps that must execute in order (e.g., provision infrastructure then deploy service).

**Split into:** One item per step with an explicit dependency chain.
- Item 1: Provision infrastructure (outcome: resources exist)
- Item 2: Deploy service to provisioned infrastructure (outcome: service running, dependency: Item 1)

### Multi-Environment
An outcome spans multiple environments (e.g., deploy to staging then production).

**Split into:** One item per environment.
- Item 1: Deploy to staging (outcome: service running in staging)
- Item 2: Deploy to production (outcome: service running in production, dependency: Item 1 verified)

---

## Validators (Quality Gates)

Validators are strict and non-prescriptive.

They:
- Do not redesign artifacts
- Do not suggest solutions
- Do not infer missing information
- Evaluate only what is explicitly present

If required information is missing or ambiguous, the validator must FAIL.

---

## Validator Responsibilities

### PRD Validator

Confirms:
- Clear problem statement
- Explicit goals and success criteria
- Defined scope and non-goals
- Documented constraints and assumptions

---

### ACF Validator

Confirms:
- Platform assumptions are stated
- Security guardrails are defined
- Reliability and observability expectations are stated
- Forbidden patterns are listed
- Constraints are enforceable

---

### SAD Validator

Confirms:
- System boundaries are defined
- Major components are identified
- Architectural decisions are documented
- ACF guardrails are respected

---

### DCF Validator

Confirms:
- Design principles are defined
- Quality bars are measurable
- Non-goals enforcement rules are present
- Testing and operational expectations are stated
- Standards are enforceable

---

### TDD Validator

Confirms:
- Interfaces and contracts are explicit; cross-boundary interfaces are stub-ready
- Build and deployment steps are defined
- Failure and rollback behavior is specified
- Test strategy is clear
- No scope expansion beyond SAD
- No violation of stated non-goals

---

### WDD Validator

Confirms:
- One outcome per item
- Explicit inputs and outputs
- No remaining design decisions
- No cross-environment execution
- No scope added beyond TDD
- Every item assigned to a work group with business-testable capability

---

### Definition of Ready (DoR) Validator

Confirms WDD work items are:
- Traceable to TDD
- Atomic and executable
- Unambiguous
- AI-safe
- Ready for immediate execution

---

### Consistency Validator

Verifies cross-artifact relationships:
- Every PRD requirement traces to SAD components and WDD work items
- No downstream artifact introduces scope absent from upstream
- No downstream artifact contradicts a stated non-goal
- ACF constraints are respected in all technical artifacts
- SAD integration points align with TDD specifications
- Frozen addendum changes are reflected downstream
- SAD boundaries, data ownership, and failure modes align with TDD specifications

---

### Impact Analysis Validator

Verifies the completeness and accuracy of a change impact analysis report:
- Every downstream artifact in the chain below the change target is accounted for
- Affected WDD work items are identified with severity classification
- Constraint and non-goal propagation is traced where applicable
- Downstream addendum conflicts are assessed
- Report structure is complete with all required fields

---

### ORD Validator

Confirms:
- Deployment verified with evidence (per TDD §5)
- Observability verified with evidence (per TDD §7, ACF §6)
- Alerting and monitoring verified with evidence (per DCF §5)
- Failure handling tested with evidence (per TDD §6)
- Security guardrails verified with evidence (per ACF §3)
- Runbook procedures documented and tested (per TDD §9)
- No open items blocking production
- All evidence is concrete, not assertions

---

## Human and AI Responsibilities

### During Planning (Product Brief → WDD Freeze)
- **AI** drafts artifacts and runs validators
- **Humans** approve promotion and resolve ambiguity
- **Humans** make trade-offs and exceptions

### During Execution (WDD Freeze → Done)
- **AI** generates tests, proposes plans, writes code, reviews diffs
- **Humans** approve test specs, approve plans, review PRs, make tradeoff decisions
- **AI** flags risks, ambiguity, and scope drift
- **Humans** resolve risks, clarify requirements, merge PRs

AI assists. Humans decide.

---

## AI Safety Rules

These rules apply across all phases — planning and execution:

- No secrets, credentials, or sensitive data in prompts
- No open-ended generation — output is scoped to the current artifact or WDD work item
- No unresolved decisions — if something is unclear, ask
- No undocumented assumptions — state what you assumed
- No scope expansion — if new work is discovered, it goes back to the appropriate artifact, not into the current output

---

## Enforcement Model

This playbook assumes mechanical enforcement where possible:

- Validators block promotion
- Workflow states enforce freeze points
- Execution systems block non-ready work

Manual overrides are allowed but must be explicit and intentional.

---

## Outcomes

When followed consistently, this playbook produces:

- Predictable AI output
- Reduced rework
- Clear ownership
- Faster execution
- Audit-ready artifacts
- Safer AI usage at scale

---

## Amendment Procedure

A frozen artifact may be corrected in place without re-validation when **all** of the following criteria are met:

1. The correction does not affect any field evaluated by a hard gate.
2. The correction does not change scope, decisions, owners, or technical specifications.
3. The correction does not affect any field referenced by a downstream artifact.

**Procedure:** Make the correction and add an Amendment Log entry to the artifact's Document Control section: date, what changed, materiality criterion cited, and who authorized the change. No re-validation is required.

**If there is any ambiguity** about whether a change is non-material, treat it as material and use the Re-Entry Protocol. The amendment path must not become a workaround for the re-entry protocol.

---

## Escalation Paths

This kit produces artifacts that may be the destination of escalations from downstream layers, and may also be the source of escalations (if a rollback reveals a product direction problem, that escalation goes upstream to PIK).

### Receiving Escalations

**Trigger 1 — SEV1/2 Incident with Code Defect (from RRK)**

**Signal:** The Reliability & Resilience Kit (Layer 6) has identified a SEV1 or SEV2 incident whose root cause is a code defect in a system built by this kit.

**What to do:**
1. Receive the escalation record from the RRK team. It will include: trigger type, triggering IR ID, the code defect description, and recommended action.
2. Create a Kit Entry Record (Step 0) for the expedited re-entry. The escalation record is the justification for the entry gate. Mark the classification as Maintenance (defect fix) with urgency level based on severity.
3. The KER fast-tracks to immediate execution: the defect description and affected system are the PRD-equivalent input. No full PRD cycle is required for a well-scoped defect fix — the KER documents this explicitly.
4. Proceed from KER → SAD (if the defect is architectural) or KER → TDD (if the defect is implementation-level) or KER → WDD (if the defect is a single bounded fix). Start at the appropriate level.
5. Execute, validate, produce an ORD for the fix, and deliver to REK for a patch release.
6. Reference the originating IR ID in the KER and all downstream artifacts for traceability.

**Trigger 3 — Release Rollback (from REK)**

**Signal:** The Release & Exposure Kit (Layer 5) has rolled back a release and identified that the rollback cause was a code or build defect.

**What to do:**
1. Receive the escalation record from the REK team. It will include: trigger type, triggering RR ID, the rollback cause description, and recommended action.
2. Create a Kit Entry Record for the defect fix. Reference the RR ID.
3. Follow the same path as Trigger 1 above, scaled to the severity of the rollback cause.
4. Coordinate with REK on the re-release: the fixed system must produce a new ORD and re-enter the REK release process.

---

## Principle File Revision

When a principle file in `docs/principles/` changes, use the change categories defined in `aieos-spec/docs/principle-file-standard.md`:

| Change Category | Version Bump | Re-Entry Impact |
|----------------|-------------|-----------------|
| **Minor** (clarification only) | `v_.x → v_.x+1` | No re-entry required; already-frozen artifacts remain valid |
| **Significant** (new requirement or tightened constraint) | `v1.x → v1.x+1` | Review artifacts generated after the change against updated principles; already-frozen artifacts are grandfathered |
| **Breaking** (removal or loosening) | `vN.x → vN+1.0` | Requires service owner authorization and documented business justification; re-entry may be warranted |

Every change to a principle file must bump the version field, even minor clarifications.

---

## Deprecation and Sunset

When an engineering engagement ends — either by completing delivery, being cancelled, or being superseded — the artifacts it produced transition to terminal lifecycle states. See the full protocol in `aieos-spec/docs/deprecation-protocol.md`.

### When to Deprecate or Abandon

| Situation | Action |
|-----------|--------|
| Engagement completed; ORD frozen and handed off to REK | No immediate action — artifacts become Deprecated when the system reaches end of life |
| System decommissioned after production operation | `Deprecated` on all frozen EEK artifacts for that system |
| Engagement cancelled after one or more artifacts are Frozen | `Deprecated` on frozen artifacts; `Abandoned` on any non-frozen artifacts |
| Engagement cancelled before any artifact is Frozen | `Abandoned` on all in-progress artifacts |

### Who Authorizes

An engineering lead, team lead, or equivalent role must authorize the terminal state transition. Do not move to `Deprecated` or `Abandoned` without an authorizing name and role recorded in the Deprecation Notice.

### How to Issue a Deprecation Notice (DN)

1. Confirm the cancellation or conclusion decision is authorized.
2. List all artifacts in the engagement series (KER, PRD, ACF, SAD, DCF, TDD, WDD, ORD) with their current status.
3. For each artifact: if Frozen → `Deprecated`; if not Frozen → `Abandoned`.
4. Create a DN record at `docs/sdlc/dn-{project-id}-{NNN}.md` using the format in `aieos-spec/docs/deprecation-protocol.md`.
5. Update each artifact's Status field to its terminal state (non-material amendment; add Amendment Log entry per governance model §6).

### What Does Not Change

- Artifacts are retained — never deleted.
- Terminal state does not require re-validation.
- If the work restarts under a revised PRD, new artifacts with new IDs are produced. Terminal-state artifacts are not reactivated.

---

## Final Note

This system is intentionally disciplined.

The structure exists to prevent downstream chaos, not to slow delivery.

If something feels heavy, it is usually protecting execution.
If something feels light, it has been deliberately constrained.
