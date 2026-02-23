# Artifact Specifications

This directory contains the **authoritative content rules and quality criteria** for each artifact in the ai-sdlc-kit lifecycle.

Specifications define **what makes an artifact good** — independent of who produces it (human or AI) and how it is evaluated.

They work in concert with:
- Templates (structure)
- Prompts (AI generation behavior)
- Validators (pass/fail judgment)

Together, they form a **four-file system** where each file answers exactly one question:

| File | Question |
|------|----------|
| **Spec** (`{type}-spec.md`) | What makes this artifact good? |
| **Template** (`{type}-template.md`) | What does the blank form look like? |
| **Prompt** (`{type}-prompt.md`) | How should AI behave when generating? |
| **Validator** (`{type}-validator.md`) | How to judge pass/fail? |

---

## What Specs Define

Specs are the single source of truth for:
- **Content rules** — what each section must contain, with failure examples
- **Format requirements** — required formats (Mermaid diagrams, Given/When/Then, etc.)
- **Completeness rules** — what "complete" means for this artifact
- **Relationship rules** — cross-artifact traceability, scope containment, non-goal enforcement
- **Hard gates** — the definitive list that validators evaluate against

---

## What Specs Do NOT Define

- **Structure** — that is the template's job (section order, field names, table shapes)
- **AI behavior** — that is the prompt's job (role, generation procedure, behavioral constraints)
- **Judgment procedure** — that is the validator's job (output format, decision rules)

---

## How to Use Specs

### When generating an artifact
Provide the spec alongside the prompt and template to the AI. The spec tells the AI what quality criteria the content must satisfy. The prompt tells the AI how to behave. The template tells the AI what form to fill in.

### When validating an artifact
Provide the spec alongside the validator to the AI. The spec tells the validator what to evaluate against. The validator tells the AI how to judge and what output to produce.

### When reviewing as a human
Read the spec directly. It describes what a good artifact looks like in plain language, without AI instructions or judgment procedures.

---

## Spec Index

### Core Artifacts
- `prd-spec.md` — Product Requirements Document
- `acf-spec.md` — Architecture Context File
- `sad-spec.md` — System Architecture Design
- `dcf-spec.md` — Design Context File
- `tdd-spec.md` — Technical Design Document
- `wdd-spec.md` — Work Design Document
- `ord-spec.md` — Operational Readiness Document

### Quality Gates
- `dor-spec.md` — Definition of Ready (per WDD work item)
- `consistency-spec.md` — Cross-artifact consistency check
- `impact-analysis-spec.md` — Change impact analysis
- `execution-spec.md` — Execution phase rules (Test / Plan / Code / Review)

---

## Rules for Spec Authors

- Specs define rules, not suggestions
- Every rule should have at least one failure example
- Do not duplicate content from templates (structure) or prompts (behavior)
- Do not add judgment procedure — that belongs in the validator
- Hard gates listed in the spec must match hard gates in the corresponding validator
- If a rule applies to multiple artifact types, it belongs in the spec for each type (not in a shared file)
