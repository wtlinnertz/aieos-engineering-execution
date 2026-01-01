# AI-Assisted Architecture-to-Execution Playbook

This playbook defines a **repeatable, AI-safe process** for moving from **product intent** to **execution-ready work** across the full Software Development Lifecycle (SDLC).

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

- PRD
  → PRD Validator
  → Freeze

- PRD + ACF
  → SAD
  → SAD Validator
  → Freeze

- SAD + DCF
  → TDD
  → TDD Validator
  → Freeze

- TDD
  → WDD
  → Sanity & Scope Check
  → Freeze

- WDD
  → Jira Stories
  → Definition of Ready (DoR)
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
- **Jira Story** — Is this ready to execute now?

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
- **Jira Story Ready** — Execution locked

Breaking a freeze requires explicit re-entry to the prior stage.

---

## Intent Summary Pre-Pass (Mandatory)

Before generating **SAD**, **TDD**, or **WDD**, an Intent Summary pre-pass must be performed.

### Purpose
- Confirm AI understanding
- Surface misalignment early
- Prevent silent scope expansion

### Rules
- The Intent Summary is not persisted
- No requirements may be added
- No design may be proposed

### Standard Prompt

Summarize the intent, constraints, and non-goals in 8–12 bullets.
Do not generate the artifact yet.
Do not add requirements or design.

Generation proceeds only after human confirmation.

---

## Refinement Ladders

### PRD Refinement Ladder

1. Author PRD
2. Run PRD Validator
3. Fix blocking issues only
4. Freeze PRD

---

### SAD Refinement Ladder

1. Run Intent Summary pre-pass
2. Generate SAD from template
3. Run SAD Validator
4. Fix blocking issues only
5. Freeze SAD

---

### TDD Refinement Ladder

1. Run Intent Summary pre-pass
2. Generate TDD from template
3. Run TDD Validator
4. Fix blocking issues only
5. Freeze TDD

---

### WDD Refinement Ladder

1. Run Intent Summary pre-pass
2. Generate WDD from template
3. Perform sanity and granularity checks
4. Freeze WDD

---

### Jira Story Refinement Ladder

1. Generate stories from WDD
2. Run Definition of Ready (DoR)
3. Fix until READY
4. Execute

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

### SAD Validator

Confirms:
- System boundaries are defined
- Major components are identified
- Architectural decisions are documented
- ACF guardrails are respected

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

### WDD Sanity and Scope Check

Confirms:
- One outcome per item
- Explicit inputs and outputs
- No remaining design decisions
- No cross-environment execution
- No scope added beyond TDD

---

### Definition of Ready (DoR) Validator

Confirms Jira stories are:
- Traceable to WDD and TDD
- Atomic and executable
- Unambiguous
- AI-safe
- Ready for immediate execution

---

## Granularity Rules (WDD and Jira)

Executable work must satisfy all of the following:

- One observable outcome
- One repository or subsystem
- One pull request
- One environment
- No design decisions
- Bounded execution time

Work that violates these rules must be split.

---

## Human and AI Responsibilities

- AI drafts artifacts and runs validators
- Humans approve promotion and resolve ambiguity
- Humans make trade-offs and exceptions
- Execution may be performed by AI, humans, or both

AI assists. Humans decide.

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
