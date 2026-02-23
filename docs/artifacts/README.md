# Artifact Templates

This directory contains **lean, AI-first templates** for each artifact in the AI-assisted software delivery lifecycle.

Each template is designed to:
- Be passed directly to an AI
- Produce consistent, structured output
- Be easy to validate
- Prevent downstream scope expansion

---

## How to Use These Templates

For each SDLC stage:

1. Start with the **upstream frozen artifact**
2. Provide the AI with:
   - The upstream artifact
   - Any applicable context files (ACF, DCF)
   - The relevant template from this directory
4. Generate the artifact
5. Run the corresponding validator
6. Fix blocking issues only
7. Freeze and promote the artifact

---

## Template Index

### Intake
- `product-brief-template.md` — Structured intake form for PRD generation (human-authored)
- `architecture-context-template.md` — Lightweight intake form for ACF generation (human-authored or pre-filled from codebase analysis)
- `design-context-template.md` — Lightweight intake form for DCF generation (human-authored or pre-filled from codebase analysis)
- `system-context-template.md` — Lightweight intake form for SAD generation in brownfield projects (human-authored or pre-filled from codebase analysis)

### Context & Requirements
- `prd-template.md` — Product intent, scope, and success criteria
- `acf-template.md` — Architecture guardrails and constraints
- `dcf-template.md` — Design standards and quality expectations

---

### Architecture & Design
- `sad-template.md` — System-level architecture and boundaries
- `adr-template.md` — Individual architectural decisions

---

### Delivery & Execution
- `tdd-template.md` — Buildable technical design
- `wdd-template.md` — Atomic, executable work items (execution-ready)

---

### Operational Readiness
- `ord-template.md` — Operational readiness verification (evidence-based)

---

## Templates vs Specs

Templates define **structure** — section order, field names, table shapes. They answer: "What does the blank form look like?"

Specs (in `docs/specs/`) define **content rules** — what each section must contain, format requirements, hard gates. They answer: "What makes this artifact good?"

Templates do not contain inline quality rules or self-check checklists. Those belong in specs.

## Rules and Expectations

- Templates must be used **as-is** unless explicitly extended
- Downstream artifacts may not reinterpret or expand upstream intent
- Anything not explicitly in scope is out of scope by default
- Non-goals are enforceable

These rules are enforced by validators defined in `docs/validators/`, which evaluate against specs defined in `docs/specs/`.

---

## Notes

- Templates are intentionally minimal
- Additional sections should only be added when they are universally required
- Organization- or tool-specific fields do not belong here

If a template feels “light,” it is likely by design.

