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

## Canonical Artifact Flow

The SDLC flow is linear and gated:

- Product Brief (human intake)
  → PRD
  → PRD Validator
  → Freeze

- ACF
  → ACF Validator
  → Freeze

- PRD + ACF
  → SAD
  → SAD Validator
  → Freeze

- DCF
  → DCF Validator
  → Freeze

- SAD + DCF
  → TDD
  → TDD Validator
  → Freeze

- TDD
  → WDD
  → WDD Validator (document-level: scope, structure, granularity)
  → Freeze

- WDD (frozen)
  → DoR Validator (per work item: readiness, traceability, AI safety)
  → Execute

- Execute (per work item):
  → Tests (`test-prompt.md` → human approves specs)
  → Plan (`plan-prompt.md` → human approves plan)
  → Code (approved plan → tests pass)
  → Review (`review-prompt.md` → human approves PR)

- Execute (work group complete)
  → Business Acceptance Testing (group-level criteria)

- Execute (all work items and groups complete)
  → ORD (`ord-prompt.md`)
  → ORD Validator
  → Production Ready

No stage may be skipped.

---

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
- **SAD Freeze** — Architecture locked
- **TDD Freeze** — Technical design locked
- **WDD Freeze** — Work scope locked
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

## Refinement Ladders

### PRD Refinement Ladder

1. Complete Product Brief (human intake)
2. Generate PRD from template using Product Brief as input
3. Run PRD Validator
4. Fix blocking issues only
5. Freeze PRD

---

### SAD Refinement Ladder

1. Generate SAD from template (intent verified inline)
2. Run SAD Validator
3. Fix blocking issues only
4. Freeze SAD

---

### TDD Refinement Ladder

1. Generate TDD from template (intent verified inline)
2. Run TDD Validator
3. Fix blocking issues only
4. Freeze TDD

---

### WDD Refinement Ladder

1. Generate WDD from template (intent verified inline)
2. Run WDD Validator
3. Fix blocking issues only
4. Freeze WDD

---

### Execution Readiness (After WDD Freeze)

1. Run DoR Validator against each WDD work item individually
2. Fix blocking issues only (if fixes require WDD changes, re-validate via Re-entry Protocol)
3. Begin execution (see Execution Loop below)

---

### ORD Refinement Ladder (After Execution)

1. Generate ORD from template using TDD, ACF, and DCF as inputs
2. Gather evidence for each verification item
3. Run ORD Validator
4. Fix blocking issues only
5. Approve ORD — system is production ready

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

## Work Groups and Business Acceptance Testing

Work groups organize related WDD items into **business-testable capabilities**. They enable incremental business acceptance testing (BAT) before the entire TDD scope is complete.

### How Work Groups Work

- Work groups are defined during WDD generation and frozen with the WDD
- Each group names its member items and states what business capability becomes testable when all items are complete
- Groups are organizational labels — they do not change item scope, granularity, or execution order
- Every work item belongs to exactly one group

### Business Acceptance Testing (BAT)

When all items in a work group are complete (all PRs merged, all tests passing), the group's business-level acceptance criteria can be tested:

1. All member items pass their individual completion criteria
2. Group-level acceptance criteria are verified (business capability works end-to-end)
3. Results are recorded as evidence

BAT is not a gate for individual item execution. It is a checkpoint between item-level completion and full ORD readiness.

---

## The Execution Loop

Once a WDD work item passes the DoR Validator, it enters the execution loop:

**Tests → Plan → Code → Review → Done**

Each phase has a prompt that drives AI behavior and a gate that controls progression. Skipping phases weakens safety.

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

**Rules:**
- Follow the approved plan
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
