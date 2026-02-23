# Engineering & Product Principles

This directory contains **organizational standards** that define how your organization writes code and builds products.

These files are **input material for ACF and DCF generation** — they are not artifact specs.

---

## What Principles Are

Principles define organizational policy:
- How code should be structured, tested, and maintained
- How product artifacts should be framed and scoped
- What engineering and product quality standards the organization enforces

They answer: **"What standards does this organization hold?"**

## What Principles Are Not

Principles are **not** artifact specifications. They do not define:
- What makes a SAD or TDD *document* good (that's what specs do)
- How AI should behave when generating (that's what prompts do)
- How to judge pass/fail (that's what validators do)

## How Principles Fit the Kit

The kit's architecture already has a home for organizational standards:
- **ACF** (Architecture Context File) — captures architecture guardrails
- **DCF** (Design Context File) — captures design standards and quality bars

When generating your ACF and DCF, use these principles as source material. The principles define policy; the ACF and DCF translate that policy into enforceable guardrails for the SDLC flow.

```
Engineering Principles → ACF (architecture guardrails) + DCF (design standards)
Product Principles     → PRD prompt (generation behavior) + ACF/DCF

ACF + DCF → SAD, TDD, WDD (constrained by guardrails)
         → Execution (code reviewed against ACF security + DCF quality bars)
```

## Mapping: Principles → Kit Artifacts

### Engineering Standards (`code-craftsmanship.md`)

| Principle Section | Feeds Into |
|---|---|
| §1 Core Engineering Doctrine (SRP, naming, DRY, complexity) | DCF §2 Design Principles |
| §2 Architecture Standards | ACF (architecture guardrails) |
| §3 Test Design Standards | DCF §6 Testing Expectations |
| §4 Implementation Standards | DCF §3 Quality Bars |
| §5 Red Flag Patterns | ACF §8 Forbidden Patterns / DCF Non-Goals |
| §6 Refactor Triggers | DCF §3 Quality Bars |
| §7 AI-Assisted Dev Rules | Execution prompts (code-prompt.md, review-prompt.md) |
| §8 Definition of Done Addendum | WDD Definition of Done |

### Product Standards (`product-craftsmanship.md`)

| Principle Section | Feeds Into |
|---|---|
| Work Classification | PRD prompt (problem framing and rigor scaling) |
| Diagnose Before Prescribing | PRD prompt, PRD §1 Problem Statement |
| Outcomes Over Output | PRD prompt, PRD §2 Goals, §10 Acceptance Criteria |
| Explicit Assumptions | PRD prompt, PRD §7 Assumptions |
| Strategic Alignment | PRD prompt, PRD §1 Problem Statement (rationale) |
| Clear Scope Boundaries | PRD prompt, PRD §3 Non-Goals, §8 Out of Scope |
| Traceability | Consistency check, downstream artifact flow |
| Evidence Over Opinion | PRD prompt, ORD evidence standards |

## Principles Index

- `code-craftsmanship.md` — Engineering standards: code quality, architecture, testing, security, AI development
- `product-craftsmanship.md` — Product standards: problem diagnosis, outcomes, assumptions, scope, traceability
