# aieos-engineering-execution Documentation

Welcome to **aieos-engineering-execution** — a practical toolkit for using AI safely across the full **software development lifecycle (SDLC)**.

This documentation explains **how to use the kit**, **why it works**, and **how the pieces fit together**.

---

## Start Here

If you’re new to the kit, follow this order:

1. **Understand the process**
   - Read the **Playbook** to learn the end-to-end flow, gates, and freeze points.

2. **Use the templates**
   - Generate SDLC artifacts using the **AI-first templates**.

3. **Apply validators**
   - Run validators to determine readiness before promotion.

4. **Learn by example**
   - Walk through the anonymized, end-to-end examples.

---

## Core Concepts

The kit is built on a few simple ideas:

- **AI generates, validators decide**
- **Artifacts are promoted, not rewritten**
- **Execution artifacts are stricter than design artifacts**
- **Ambiguity is a failure condition**
- **Non-goals are enforceable**

These principles are applied consistently across all artifacts and validators.

---

## Documentation Sections

### 📐 Governance Model
Defines the structural rules, taxonomy, and invariants for the AIEOS kit system:
- Four-file system (spec, template, prompt, validator)
- Repository structure and naming conventions
- Artifact promotion model and freeze semantics
- Inter-kit handoff contracts
- Kit invariants and adoption guidance

➡️ See: `governance-model.md`

---

### 📘 Playbook
Defines the canonical process from product intent to delivered work:
- Artifact flow and promotion rules
- Refinement ladders and freeze points
- Intent verification (built into prompts)
- Execution loop: Tests → Plan → Code → Review
- Completion criteria and enforcement responsibilities

➡️ See: `playbook.md`

---

### 🏗️ Engineering & Product Principles
Organizational standards that inform ACF and DCF creation:
- Engineering standards (code quality, testing, architecture, security)
- Product standards (problem diagnosis, outcomes, scope, traceability)

➡️ See: `principles/`

---

### 📋 Artifact Specifications
Authoritative content rules and quality criteria for each artifact:
- Define what makes an artifact good (content rules, format requirements, hard gates)
- Single source of truth for quality criteria — referenced by prompts and validators
- Human-readable without AI instructions or judgment procedures

➡️ See: `specs/`

---

### 📄 Artifact Templates
Lean, AI-first templates for each SDLC stage (structure only):
- PRD
- ACF
- SAD
- DCF
- TDD
- WDD
- ORD

Templates are designed to:
- Be passed directly to AI
- Produce consistent structure
- Be easy to validate

➡️ See: `artifacts/`

---

### 🔗 Utility Prompts
Non-governed analytical supplements:
- `codebase-analysis-prompt.md` — Brownfield system analysis; pre-fills ACF, DCF, SAD intake forms
- `impact-analysis-prompt.md` — Downstream impact analysis for frozen artifact changes
- `dprd-consistency-check-prompt.md` — Cross-boundary consistency check: Path A (DPRD provenance) or Path B (Product Brief scope) at EEK entry

➡️ See: `prompts/`

---

### ✅ Validators
Strict, non-prescriptive readiness gates:
- PRD Validator
- ACF Validator
- SAD Validator
- DCF Validator
- TDD Validator
- WDD Validator
- Definition of Ready (DoR) Validator
- Consistency Validator (cross-artifact traceability)
- Impact Analysis Validator (change impact assessment)
- ORD Validator

Validators determine **readiness**, not correctness or implementation quality.

➡️ See: `validators/`

---

### 🧪 Examples
Anonymized examples showing:
- Artifact evolution from PRD to execution
- Validator PASS/FAIL outputs
- What “good” looks like in practice

➡️ See: `../examples/`

---

## How to Use This Kit with AI

Each artifact is generated and validated in separate AI sessions:

1. **Generate** — Provide the AI with the spec, prompt, template, and frozen upstream artifacts
2. **Validate** — In a new session, provide the spec, validator, and the generated artifact
3. **Freeze** — Once PASS and human-approved, lock the artifact and move on

The human orchestrates the flow. The AI generates and validates one artifact at a time.

For detailed guidance, session examples, and common mistakes to avoid, see: `how-to-use-with-ai.md`

---

## Anonymization & Safety

This is a public repository.

All examples and templates:
- Avoid real company names
- Use placeholders for identifiers
- Remain tool- and employer-neutral

Before contributing, read:
- `ANONYMIZATION.md`
- `TERMS.md`

---

## Contributing

Contributions are welcome.

Please see:
- `CONTRIBUTING.md`
- `CODE_OF_CONDUCT.md`
- `SECURITY.md`

Small, focused improvements are preferred.

---

## Guides

- [`session-setup.md`](session-setup.md) — Per-artifact setup checklists, pre-flight gate checks, and common failure reminders
- [`troubleshooting.md`](troubleshooting.md) — Gate failure remediation guide
- [`entry-from-pik.md`](entry-from-pik.md) — Boundary briefing when arriving from the Product Intelligence Kit (Path A)

---

## Where to Go Next

- Read the **Playbook** → `playbook.md`
- Review **Specifications** → `specs/`
- Browse **Templates** → `artifacts/`
- Review **Validators** → `validators/`
- Explore **Examples** → `../examples/`

---

This kit is intentionally disciplined.

The structure you see here exists to make AI-assisted delivery **predictable, safe, and repeatable**.
