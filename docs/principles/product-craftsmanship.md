# Product Craftsmanship Principles

This document defines the non-negotiable standards for creating product artifacts
(PRDs, briefs, proposals, epics, and feature definitions) within the SDLC AI system.

These principles are always in effect and guide all product generation
and validation steps.

Product discipline is to requirements what clean code is to implementation.

The depth of rigor scales with the scope and risk of the work, but intent must
always be explicit.

---

## Work Classification

All work must declare a type:

- **Initiative** — Net-new capability or major strategic investment.
- **Enhancement** — Incremental feature improvement or version expansion.
- **Maintenance** — Bug fixes, refactoring, reliability, security, or operational work.

The required depth of analysis scales proportionally:

- Initiatives require full strategic, outcome, and assumption articulation.
- Enhancements must define incremental impact and reference existing product intent.
- Maintenance work must clearly state the behavior being restored, risk mitigated, or reliability improved.

No work proceeds without a clearly defined intent.

---

## 1. Diagnose Before Prescribing

Never begin with a solution.

Every product artifact must articulate:

- The problem or condition being addressed
- Evidence or signal supporting action
- Why action is required now

For maintenance work, the diagnosis may be:
- A defect
- A reliability regression
- A security exposure
- A performance degradation

Feature-first thinking is a defect.

---

## 2. Outcomes Over Output

Shipping features is not success.

Every product artifact must define:

- The intended outcome
- Measurable success criteria (when applicable)
- A clear definition of success or resolution

For maintenance work, the outcome may be:
- Restored SLA compliance
- Reduced incident frequency
- Reduced risk exposure
- Improved performance metrics

Outputs (features, endpoints, screens) are implementation details.
Outcomes define impact.

---

## 3. Explicit Assumptions

All product decisions rest on assumptions.

Artifacts must:

- Identify key value or feasibility assumptions (as appropriate)
- Highlight meaningful uncertainties
- Surface risk when present

For low-risk maintenance work, assumptions may be minimal.
Hidden assumptions are unacceptable.

---

## 4. Strategic Alignment

Work must trace to a broader objective, including:

- Business growth
- Customer satisfaction
- Risk reduction
- Cost efficiency
- Operational stability

If alignment cannot be explained clearly, the work should not proceed.

---

## 5. Clear Scope Boundaries

Ambiguity creates waste.

Artifacts must define:

- What is in scope
- What is out of scope
- Constraints and limitations

Unbounded scope is a design failure.

---

## 6. Simplicity as a Constraint

Complexity multiplies cost.

Solutions should:

- Address the smallest meaningful unit of impact
- Avoid speculative extensibility
- Prefer reversible decisions when possible

If a simpler version can achieve the intended outcome, build that first.

---

## 7. Traceability Across the SDLC

Product intent must survive implementation.

Artifacts must enable traceability to:

- Architectural decisions
- Implementation tasks
- Deployment metrics
- Post-release validation

If impact cannot be observed or verified,
the requirement was incomplete.

---

## 8. Evidence Over Opinion

Strong opinions require strong evidence.

Product artifacts should prioritize:

- Data
- Measurable signals
- Validated feedback
- Risk assessment

Authority alone is insufficient justification.

---

# Enforcement Model

These principles are enforced through:

- PRD spec (`prd-spec.md`) — content rules and quality criteria
- PRD prompt (`prd-prompt.md`) — AI generation behavior
- PRD validator (`prd-validator.md`) — pass/fail judgment

Validation strictness scales with work classification.

All work requires explicit intent.