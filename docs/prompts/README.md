# AI Generation Prompts

This directory contains the **canonical AI generation prompts** for each artifact in the ai-sdlc-kit lifecycle.

These prompts define **how AI behaves** when creating artifacts.
They work in concert with:
- Templates (shape)
- Validators (truth)

Together, they form a **safe, deterministic AI delivery system**.

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
3. Run the **Intent Summary pre-pass** (where required)
4. Invoke the **canonical generation prompt**
5. Generate the artifact using the **official template**
6. Run the **corresponding validator**
7. Fix blocking issues only
8. Freeze and promote the artifact

Skipping steps weakens enforcement.

---

## Prompt Index (Canonical Order)

### Requirements & Context
- `prd-prompt.md` — Generate a Product Requirements Document
- `acf-prompt.md` — Generate architectural guardrails and constraints

---

### Architecture & Design
- `sad-prompt.md` — Generate system architecture (requires intent pre-pass)
- `dcf-prompt.md` — Generate design standards and quality expectations

---

### Delivery & Execution
- `tdd-prompt.md` — Generate technical design (requires intent pre-pass)
- `wdd-prompt.md` — Generate atomic executable work (requires intent pre-pass)
- `story-prompt.md` — Generate execution-ready stories

---

## Intent Summary Pre-Pass

The following artifacts **require** an Intent Summary pre-pass:
- SAD
- TDD
- WDD

The pre-pass:
- Confirms AI understanding
- Prevents silent scope expansion
- Is not persisted as an artifact

Generation may proceed only after human confirmation.

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

Template + Prompt + Validator
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
