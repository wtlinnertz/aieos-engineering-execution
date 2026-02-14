# Validators

Validators are **strict quality gates** that determine whether an artifact is ready to be promoted to the next stage.

They are a core enforcement mechanism of **ai-sdlc-kit**.

---

## What Validators Are

Validators:
- Evaluate readiness, not correctness
- Are non-prescriptive
- Do not redesign artifacts
- Do not infer missing information
- Fail on ambiguity

If required information is missing or unclear, the validator must FAIL.

---

## What Validators Are Not

Validators do NOT:
- Suggest solutions
- Fill in gaps
- Make architectural decisions
- Improve wording
- Optimize designs

They exist to answer one question only:

> **Is this artifact ready to be promoted?**

---

## Validator Principles

All validators follow these rules:

- **AI generates, validators decide**
- **Artifacts are promoted, not rewritten**
- **Downstream scope expansion is forbidden**
- **Non-goals are enforceable**
- **Ambiguity is a failure condition**

---

## Validator Execution Model

For each artifact:

1. Generate the artifact using the appropriate template
2. Run the corresponding validator
3. If the validator FAILS:
   - Fix blocking issues only
   - Re-run the validator
4. If the validator PASSES:
   - Freeze the artifact
   - Promote to the next stage

---

## Validator Index

### Context & Requirements
- `prd-validator.md`
- `acf-validator.md`

---

### Architecture & Design
- `sad-validator.md`
- `dcf-validator.md`
- `tdd-validator.md`

---

### Delivery & Execution
- `wdd-validator.md`
- `dor-validator.md`

---

## Enforcement Notes

Validators are intended to be:
- Run manually
- Run by AI agents
- Integrated into CI / workflow gates
- Used to block workflow transitions

Manual overrides are allowed but must be explicit and intentional.

---

## Notes

- Validators should evolve conservatively
- Reducing false positives is preferred over adding new gates
- Any change that weakens enforcement should be treated as breaking
