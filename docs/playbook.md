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

## Canonical Artifact Flow

The SDLC flow is linear and gated:

- Product Brief (human intake)
  → PRD (`prd-prompt.md`)
  → PRD Validator
  → Freeze

- ACF (`acf-prompt.md`)
  → ACF Validator
  → Freeze

- PRD + ACF
  → SAD (`sad-prompt.md`)
  → SAD Validator
  → Freeze

- DCF (`dcf-prompt.md`)
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
  → Human approval
  → Freeze
  → Execute

- Execute (per work item):
  → Tests (`test-prompt.md` → human approves specs)
  → Plan (`plan-prompt.md` → human approves plan)
  → Code (approved plan → tests pass)
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
2. **Use the prompt** — Provide the inputs and the corresponding `{type}-prompt.md` to the AI
3. **Use the template** — The AI generates output following `{type}-template.md` exactly
4. **Run the validator** — Provide the generated artifact to `{type}-validator.md`
5. **Fix blocking issues only** — If the validator returns FAIL, fix only the blocking issues and re-run
6. **Freeze** — Once the validator returns PASS, the artifact is frozen and may not be changed without re-entry

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

---

## Step 1: PRD (Product Requirements Document)

**What:** Define the problem and why it matters.

**Prompt:** `prd-prompt.md`
**Inputs:** Product Brief (human-written intake)
**Validator:** `prd-validator.md`
**Gate:** Validator PASS + human approval
**Output:** Frozen PRD

### Steps
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
**Inputs:** Organizational standards, platform constraints, security and compliance requirements
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
**Inputs:** Frozen PRD + Frozen ACF
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
**Inputs:** Organizational design standards, testing expectations, operational expectations
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

### Steps
1. Generate TDD using `tdd-prompt.md` with frozen SAD and frozen DCF as inputs
2. Run `tdd-validator.md` against the generated TDD
3. Fix blocking issues only; re-run validator until PASS
4. Human reviews and approves
5. Freeze TDD

---

## Step 6: WDD (Work Design Document)

**What:** Decompose a frozen TDD into atomic, executable work items grouped into business-testable work groups.

**Prompt:** `wdd-prompt.md`
**Inputs:** Frozen TDD
**Validator:** `wdd-validator.md` (document-level), then `dor-validator.md` (per work item)
**Gate:** Both validators PASS + human approval
**Output:** Frozen WDD with all work items passing DoR

The WDD prompt includes **intent verification**: the AI restates upstream intent in Section 1 before generating.

### Steps
1. Generate WDD using `wdd-prompt.md` with frozen TDD as input
2. Run `wdd-validator.md` against the full WDD document
3. Fix blocking issues only; re-run validator until PASS
4. Run `dor-validator.md` against each work item individually
5. Fix blocking issues only; re-run validator until PASS (if fixes require WDD changes, re-run `wdd-validator.md` as well)
6. Human reviews and approves
7. Freeze WDD
8. Begin execution

---

## Step 7: Execute (Per Work Item)

Once a WDD work item passes the DoR Validator, it enters the execution loop. See **Part 2: The Execution Loop** below.

---

## Step 8: Work Groups

When all items in a work group are complete, the group's business-level acceptance criteria can be verified. See **Work Groups and Business Acceptance Testing** below.

---

## Step 9: ORD (Operational Readiness Document)

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

Once a WDD work item passes the DoR Validator, it enters the execution loop:

**Tests → Plan → Code → Review → Done**

Each phase has a prompt that drives AI behavior and a gate that controls progression. Skipping phases weakens safety.

**Execution order:** If a work item's inputs depend on another item's outputs, the dependency must be completed first. Execute items in dependency order. Independent items may be executed in parallel.

**Cancellation:** A human may cancel a work item at any point with a recorded reason. If other items depend on the cancelled item, assess and resolve the impact before continuing. Cancellation does not require re-entry unless the WDD itself needs to change.

**Scope discovery during execution:** If implementation reveals that a work item's approach is more complex than anticipated but its scope is correct, handle it in the Plan phase — no WDD change needed. If implementation reveals that new work items are needed, existing items need different interfaces, or scope was misunderstood, trigger the Re-entry Protocol to update the WDD.

### Execution Principles

1. **Tests define targets** — If tests aren't defined, we're not ready to implement
2. **Plan before code** — Approve an approach before changing files
3. **Small, reversible steps** — Keep diffs focused; iterate with tests
4. **Verification is built into review** — Not a separate step; the review prompt checks completeness
5. **Auditability** — Track what was done, what was tested, and what was reviewed

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

---

### Phase 3: Code

**Goal:** Implement the plan. Make tests pass.

**Prompt:** None — the approved plan is the instruction
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
- If tests fail, debug with full context (error messages, stack traces, test output)
- If answers degrade or context is lost, restart with fresh context
- Multiple iterations are normal

---

### Phase 4: Review

**Goal:** Confirm the change is safe, correct, within scope, verified, and ready to merge.

**Prompt:** `review-prompt.md`
**Gate:** Human approves PR

**AI Review (First Pass):**
The review prompt checks both correctness and completeness:
- Scope adherence and interface compliance
- Test coverage (acceptance, failure, edge cases)
- Verification (all tests passing, DoD satisfied, evidence present)
- Security concerns and factual risks

**Human Review (Required):**
- AI review is advisory — human review is mandatory
- Review the PR with an explainer covering:
  - What changed and why (link to WDD item)
  - Tests added/updated and what they assert
  - Test results (pass/fail evidence)
  - Risks or known limitations
- Changes that expand scope beyond the WDD work item must be rejected

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

### Work Groups and Business Acceptance Testing

Work groups organize related WDD items into **business-testable capabilities**. They enable incremental business acceptance testing (BAT) before the entire TDD scope is complete.

#### How Work Groups Work

- Work groups are defined during WDD generation and frozen with the WDD
- Each group names its member items and states what business capability becomes testable when all items are complete
- Groups are organizational labels — they do not change item scope, granularity, or execution order
- Every work item belongs to exactly one group

#### Business Acceptance Testing (BAT)

**Status: TBD** — The BAT process (who runs it, what the output format is, and how it gates progression) is not yet defined. BAT is expected to happen after work group completion but before ORD, and will be specified in a future update.

When defined, BAT will verify:
1. All member items pass their individual completion criteria
2. Group-level acceptance criteria are satisfied (business capability works end-to-end)
3. Results are recorded as evidence

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

---

## Re-entry Protocol

When a frozen artifact must change, follow this protocol:

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
7. **Assess execution impact** — If re-entry affects a WDD with items in progress or completed, identify which work items are impacted by the change. Impacted completed items must be re-verified against the updated upstream. Impacted in-progress items must incorporate the change. Unaffected items continue as-is.

### What Re-entry Does NOT Allow
- Expanding scope beyond the original intent
- Skipping validators on the changed artifact
- Changing downstream artifacts without re-validating them

### Responsibility
- **Humans** decide whether re-entry is warranted
- **AI** may flag the need for re-entry but must not unfreeze artifacts autonomously

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
- Interfaces and contracts are explicit
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

## Final Note

This system is intentionally disciplined.

The structure exists to prevent downstream chaos, not to slow delivery.

If something feels heavy, it is usually protecting execution.
If something feels light, it has been deliberately constrained.
