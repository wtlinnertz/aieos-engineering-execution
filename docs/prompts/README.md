# AI Generation Prompts

This directory contains the **canonical AI generation prompts** for each artifact in the ai-sdlc-kit lifecycle.

These prompts define **how AI behaves** when creating artifacts.
They work in concert with:
- Specs (content rules and quality criteria)
- Templates (structure)
- Validators (judgment)

Together, they form a **four-file system** where each file answers exactly one question:

| File | Question |
|------|----------|
| **Spec** (`{type}-spec.md`) | What makes this artifact good? |
| **Template** (`{type}-template.md`) | What does the blank form look like? |
| **Prompt** (`{type}-prompt.md`) | How should AI behave when generating? |
| **Validator** (`{type}-validator.md`) | How to judge pass/fail? |

---

## What These Prompts Are

These prompts:
- Are **production-grade**
- Are safe to hand directly to AI
- Enforce single responsibility per artifact
- Prevent scope expansion during generation
- Align exactly with validators and freeze points

They are designed to be:
- Copy/pasted
- Embedded in tooling
- Used by agents

---

## What These Prompts Are Not

These prompts do **not**:
- Contain domain-specific content
- Encode organizational policy
- Include examples or defaults
- Suggest solutions or improvements
- Replace human judgment

Variable context belongs in artifacts, not prompts.

---

## How to Use These Prompts

For each artifact generation:

1. Ensure all **upstream artifacts are frozen**
2. Gather required **context files** (ACF / DCF if applicable)
3. Invoke the **canonical generation prompt** (intent verification is built in)
4. Generate the artifact using the **official template**
5. Run the **corresponding validator**
6. Fix blocking issues only
7. Freeze and promote the artifact

Skipping steps weakens enforcement.

---

## Prompt Index (Canonical Order)

### Requirements & Context
- `prd-prompt.md` — Generate a Product Requirements Document
- `acf-prompt.md` — Generate architectural guardrails and constraints

---

### Architecture & Design
- `sad-prompt.md` — Generate system architecture (intent verified inline)
- `dcf-prompt.md` — Generate design standards and quality expectations

---

### Quality & Governance
- `consistency-prompt.md` — Cross-artifact traceability and consistency check
- `impact-analysis-prompt.md` — Assess downstream ripple effects of a proposed change

---

### Delivery & Execution
- `tdd-prompt.md` — Generate technical design (intent verified inline)
- `wdd-prompt.md` — Generate atomic executable work items (intent verified inline)
- `test-prompt.md` — Generate test specifications from WDD acceptance criteria
- `plan-prompt.md` — Generate implementation plan from test specs and TDD contracts
- `code-prompt.md` — Implement approved plan, make tests pass
- `review-prompt.md` — Review and verify implementation against WDD scope, TDD contracts, and DoD
- `execution-plan-prompt.md` — Determine execution order and extract per-item context from upstream artifacts
- `execution-plan-tests-prompt.md` — Assemble ready-to-use Phase 1 (Tests) prompts per work item
- `execution-plan-plan-prompt.md` — Assemble ready-to-use Phase 2 (Plan) prompts per work item
- `execution-plan-code-prompt.md` — Assemble ready-to-use Phase 3 (Code) prompts per work item
- `execution-plan-review-prompt.md` — Assemble ready-to-use Phase 4 (Review) prompts per work item
- `ord-prompt.md` — Generate operational readiness verification from TDD/ACF/DCF

---

## Intent Verification

The following prompts include an **inline intent verification self-check**:
- SAD
- TDD
- WDD

The self-check:
- Instructs the AI to restate upstream intent in Section 1 before generating
- Stops generation if scope cannot be reconciled with upstream artifacts
- Is enforced by the downstream validator as a hard gate (`intent_integrity`, `intent_alignment`, `traceability`)

---

## Rules Shared by All Prompts

All prompts enforce the following rules:

- Do not infer missing information
- Do not redesign or optimize
- Do not expand scope
- Do not violate non-goals
- Mark missing information explicitly
- Ambiguity is a failure condition

These rules are enforced again by validators.

---

## Relationship to Validators

Prompts guide **generation behavior**.
Validators determine **readiness for promotion**.

Passing generation does **not** imply readiness.

Only validators can declare an artifact:
- READY
- FROZEN
- BLOCKED

---

## Recommended Usage Pattern

Spec + Template + Prompt + Validator
Removing any one of these reduces safety.

---

## Final Note

These prompts are intentionally strict.

They trade short-term convenience for:
- Predictability
- Auditability
- Scalability
- Safe AI usage

If a prompt feels "unhelpful," it is likely protecting downstream execution.
