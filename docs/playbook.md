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

Artifacts that answer more than one question are invalid.

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

---

### Definition of Ready (DoR) Validator

Confirms WDD work items are:
- Traceable to TDD
- Atomic and executable
- Unambiguous
- AI-safe
- Ready for immediate execution

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

## The Execution Loop

Once a WDD work item passes the DoR Validator, it enters the execution loop:

**Context → Tests → Plan → Code → Verify → Review → Done**

Each phase has explicit inputs, outputs, and rules. Skipping phases weakens safety.

### Execution Principles

1. **Context over guessing** — Give the executor the same briefing you'd give a new teammate
2. **Tests define targets** — If tests aren't defined, we're not ready to implement
3. **Plan before code** — Approve an approach before changing files
4. **Small, reversible steps** — Keep diffs focused; iterate with tests
5. **Verification matters** — Tests and reviews are part of the loop, not an afterthought
6. **Auditability** — Track what was done, what was tested, and what was reviewed

---

### Phase 1: Context

**Goal:** Assemble everything the executor needs before any code is written.

**Required Inputs:**
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

**Output:** A scoped context package ready to inform test generation and planning.

---

### Phase 2: Tests First

**Goal:** Turn acceptance criteria into verifiable test specifications before writing code.

**Required Inputs:**
- WDD acceptance criteria (Given/When/Then format)
- TDD testing strategy (test layers, pass/fail criteria)
- DCF testing expectations (required test types, evidence requirements)

**Rules:**
- Generate tests that directly reflect the WDD acceptance criteria (use `test-prompt.md`)
- Include at least one failure condition per acceptance criterion
- Include edge cases and boundary conditions
- Name tests clearly — test names should describe the scenario being verified
- Do NOT write implementation code during this phase
- Tests must be approved before proceeding to code

**Test Categories:**
1. **Acceptance tests** — Direct mapping from WDD Given/When/Then criteria
2. **Failure tests** — At least one per acceptance criterion
3. **Edge case tests** — Boundary conditions, empty inputs, invalid states
4. **Regression tests** — If fixing a bug, a test that fails before the fix and passes after

**Output:** Approved test specifications ready for implementation.

---

### Phase 3: Plan

**Goal:** Choose the smallest change that makes tests pass.

**Required Inputs:**
- Approved test specifications
- TDD interface contracts (method signatures, return types, status codes)
- Relevant source code

**Rules:**
- Propose a plan only — do not write code yet
- Identify the minimal set of files to change
- Call out tradeoffs or risks
- Preserve public interfaces unless explicitly approved to change
- Lock down observable behavior (outputs, exceptions, status codes, data shapes)
- If anything is unclear, ask before planning further

**What to Lock Down:**
- Method signatures (name, parameters, return type)
- Observable behavior (outputs, exceptions, status codes)
- Data contracts (JSON fields, schema shapes)
- Dependencies (libraries, versions — verify before adding)

**Output:** An approved implementation plan scoped to the WDD work item.

---

### Phase 4: Code

**Goal:** Implement the plan. Make tests pass.

**Rules:**
- Follow the approved plan
- Make only the minimal changes needed to pass approved tests
- Touch only files identified in the plan
- Explain each change before applying it
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

### Phase 5: Verify

**Goal:** Confirm that all acceptance criteria are met and the Definition of Done is satisfied.

**Verification Checklist:**
- [ ] All acceptance criterion tests pass
- [ ] All failure condition tests pass
- [ ] Edge case tests pass
- [ ] No regressions in existing tests
- [ ] WDD Definition of Done items are satisfied:
  - [ ] PR ready to merge
  - [ ] Tests passing (type as specified in WDD)
  - [ ] Evidence/logs generated (as specified in WDD)
- [ ] Rollback behavior is tested or verified (as specified in WDD)

**Rules:**
- Do not skip verification — partial verification is a failure condition
- If any DoD item cannot be satisfied, stop and escalate
- Evidence must be concrete (test reports, log output, screenshots) — not assertions

---

### Phase 6: Review

**Goal:** Confirm the change is safe, correct, and within scope before merging.

**AI Review (First Pass):**
Use `review-prompt.md` to review the diff for:
- Logic risks or correctness issues
- Missed edge cases
- Scope adherence (does the diff match the WDD work item?)
- Interface compliance (does it match TDD contracts?)
- Security concerns (secrets, injection, access control)

**Human Review (Required):**
- AI review is advisory — human review is mandatory
- Review the PR with an explainer covering:
  - What changed and why (link to WDD item)
  - Tests added/updated and what they assert
  - Test results (pass/fail evidence)
  - Risks or known limitations

**Rules:**
- Treat AI review suggestions as input, not decisions
- Human reviewer must verify scope adherence
- Changes that expand scope beyond the WDD work item must be rejected

---

### Execution Refinement Ladder

When quality is not yet acceptable during execution, work through these steps top-down. Multiple iterations are normal.

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
