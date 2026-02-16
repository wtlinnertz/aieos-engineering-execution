# AI-Assisted Execution Guide

This guide defines a **repeatable, safe protocol** for executing WDD work items — whether by AI agents, humans, or both.

It picks up where the playbook leaves off: after a WDD work item passes the Definition of Ready (DoR), this guide governs **how** it gets built, tested, verified, and reviewed.

---

## Purpose

AI can generate code quickly, but speed without structure leads to:

- Scope drift during implementation
- Untested or partially tested changes
- Rework from missed requirements
- Security and quality blind spots
- Unreviewable pull requests

This guide ensures that:

- Execution is constrained by the WDD work item
- Tests are defined before code is written
- Changes are small, reversible, and verifiable
- Reviews are structured, not ad-hoc
- Humans remain accountable for approval

---

## Core Principles

These principles apply to every execution:

1. **Context over guessing** — Give the executor the same briefing you'd give a new teammate
2. **Tests define targets** — If tests aren't defined, we're not ready to implement
3. **Plan before code** — Approve an approach before changing files
4. **Small, reversible steps** — Keep diffs focused; iterate with tests
5. **Verification matters** — Tests and reviews are part of the loop, not an afterthought
6. **Auditability** — Track what was done, what was tested, and what was reviewed

---

## The Execution Loop

Every WDD work item follows this loop:

**Context → Tests → Plan → Code → Verify → Review → Done**

Each phase has explicit inputs, outputs, and rules. Skipping phases weakens safety.

---

## Phase 1: Context

**Goal:** Assemble everything the executor needs before any code is written.

### Required Inputs
- WDD work item (intent, scope, inputs, outputs, acceptance criteria, DoD)
- TDD interface contracts and relevant design sections
- DCF testing and operational expectations
- Relevant source code and configuration

### Rules
- Curate context deliberately — do not dump entire codebases
- Include constraints, conventions, and patterns that apply
- Include logs, traces, or error messages if fixing a bug
- Remove or replace sensitive data with placeholders
- If requirements are unclear, clarify before proceeding — do not assume

### Output
A scoped context package ready to inform test generation and planning.

---

## Phase 2: Tests First

**Goal:** Turn acceptance criteria into verifiable test specifications before writing code.

### Required Inputs
- WDD acceptance criteria (Given/When/Then format)
- TDD testing strategy (test layers, pass/fail criteria)
- DCF testing expectations (required test types, evidence requirements)

### Rules
- Generate tests that directly reflect the WDD acceptance criteria
- Include at least one failure condition per acceptance criterion
- Include edge cases and boundary conditions
- Name tests clearly — test names should describe the scenario being verified
- Do NOT write implementation code during this phase
- Tests must be approved before proceeding to code

### Test Categories
1. **Acceptance tests** — Direct mapping from WDD Given/When/Then criteria
2. **Failure tests** — At least one per acceptance criterion (required by WDD)
3. **Edge case tests** — Boundary conditions, empty inputs, invalid states
4. **Regression tests** — If fixing a bug, a test that fails before the fix and passes after

### Output
Approved test specifications ready for implementation.

---

## Phase 3: Plan

**Goal:** Choose the smallest change that makes tests pass.

### Required Inputs
- Approved test specifications
- TDD interface contracts (method signatures, return types, status codes)
- Relevant source code

### Rules
- Propose a plan only — do not write code yet
- Identify the minimal set of files to change
- Call out tradeoffs or risks
- Preserve public interfaces unless explicitly approved to change
- Lock down observable behavior (outputs, exceptions, status codes, data shapes)
- If anything is unclear, ask before planning further

### What to Lock Down
- Method signatures (name, parameters, return type)
- Observable behavior (outputs, exceptions, status codes)
- Data contracts (JSON fields, schema shapes)
- Dependencies (libraries, versions — verify before adding)

### Output
An approved implementation plan scoped to the WDD work item.

---

## Phase 4: Code

**Goal:** Implement the plan. Make tests pass.

### Rules
- Follow the approved plan
- Make only the minimal changes needed to pass approved tests
- Touch only files identified in the plan
- Explain each change before applying it
- Keep diffs small and focused — one logical change per step
- If a dependency doesn't exist or version differs, ask before adding
- If anything is unclear, ask before coding
- Do NOT expand scope beyond the WDD work item

### Iteration
- Run tests after each change
- If tests fail, debug with full context (error messages, stack traces, test output)
- If answers degrade or context is lost, restart with fresh context
- Multiple iterations are normal

---

## Phase 5: Verify

**Goal:** Confirm that all acceptance criteria are met and the Definition of Done is satisfied.

### Verification Checklist
- [ ] All acceptance criterion tests pass
- [ ] All failure condition tests pass
- [ ] Edge case tests pass
- [ ] No regressions in existing tests
- [ ] WDD Definition of Done items are satisfied:
  - [ ] PR ready to merge
  - [ ] Tests passing (type as specified in WDD)
  - [ ] Evidence/logs generated (as specified in WDD)
- [ ] Rollback behavior is tested or verified (as specified in WDD)

### Rules
- Do not skip verification — partial verification is a failure condition
- If any DoD item cannot be satisfied, stop and escalate
- Evidence must be concrete (test reports, log output, screenshots) — not assertions

---

## Phase 6: Review

**Goal:** Confirm the change is safe, correct, and within scope before merging.

### AI Review (First Pass)
Ask the AI to review the diff for:
- Logic risks or correctness issues
- Missed edge cases
- Scope adherence (does the diff match the WDD work item?)
- Interface compliance (does it match TDD contracts?)
- Security concerns (secrets, injection, access control)

### Human Review (Required)
- AI review is advisory — human review is mandatory
- Review the PR with an explainer covering:
  - What changed and why (link to WDD item)
  - Tests added/updated and what they assert
  - Test results (pass/fail evidence)
  - Risks or known limitations

### Rules
- Treat AI review suggestions as input, not decisions
- Human reviewer must verify scope adherence
- Changes that expand scope beyond the WDD work item must be rejected

---

## Refinement Ladder

When quality is not yet acceptable, work through these steps top-down. Multiple iterations are normal.

### 1. Clarify
What assumptions were made during implementation? What details are missing? Resolve ambiguity before continuing.

### 2. Simplify
Reduce complexity. Fewer branches, less nesting, clearer flow. Keep behavior identical.

### 3. Refactor
Align with team conventions and patterns. Ensure idiomatic, maintainable code without unnecessary abstractions.

### 4. Harden
Add input validation, meaningful error messages, and error handling aligned with TDD failure handling patterns.

### 5. Explain
Add documentation where the logic isn't self-evident. Summarize design tradeoffs and risks.

---

## Completion Criteria

A WDD work item is complete when:

1. All acceptance criteria tests pass
2. All DoD checklist items are satisfied
3. PR is reviewed and approved (AI + human)
4. Evidence is generated and accessible
5. No scope expansion occurred beyond the WDD work item

---

## AI Safety Rules (During Execution)

- No secrets, credentials, or sensitive data in prompts
- No open-ended generation — execution is scoped to the WDD work item
- No unresolved decisions — if something is unclear, ask
- No undocumented assumptions — state what you assumed
- No scope expansion — if new work is discovered, it goes back to the WDD, not into the current PR

---

## Human and AI Responsibilities (During Execution)

- **AI** generates tests, proposes plans, writes code, reviews diffs
- **Humans** approve test specs, approve plans, review PRs, make tradeoff decisions
- **AI** flags risks, ambiguity, and scope drift
- **Humans** resolve risks, clarify requirements, merge PRs

AI executes. Humans decide.

---

## Relationship to Other Kit Documents

| Document | Role During Execution |
|----------|----------------------|
| WDD Work Item | Defines scope, acceptance criteria, inputs, outputs, DoD |
| TDD | Defines interface contracts, testing strategy, failure handling |
| DCF | Defines testing expectations, operational standards |
| ACF | Defines security and compliance guardrails |
| DoR Validator | Confirms work item was ready before execution began |

The execution guide does not replace these documents. It defines the protocol for consuming them during implementation.

---

## Final Note

This guide is intentionally disciplined.

The structure exists to prevent execution drift, not to slow delivery.

If a phase feels heavy, it is usually protecting the next phase.
If a phase feels light, it has been deliberately constrained.
