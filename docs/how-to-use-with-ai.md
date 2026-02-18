# How to Use ai-sdlc-kit with AI

This guide explains how to use the kit's prompts, templates, and validators with an AI assistant to generate and validate SDLC artifacts.

---

## The Core Pattern

Every artifact follows the same three-step pattern:

1. **Generate** — Give the AI a prompt and inputs; it produces the artifact
2. **Validate** — Give the AI the validator and the artifact; it returns PASS or FAIL
3. **Freeze** — Once PASS and human-approved, the artifact is locked

Each step is a separate AI session. This keeps context clean and prevents the AI from conflating artifacts.

---

## Generating an Artifact

Start a new AI session and provide:

1. **The generation prompt** — The `{type}-prompt.md` file for the artifact you're generating
2. **The template** — The `{type}-template.md` file so the AI follows the exact structure
3. **The frozen upstream artifacts** — The inputs listed in the prompt

### Example: Generating a SAD

> Here is the prompt for generating a System Architecture Design:
>
> [paste contents of `sad-prompt.md`]
>
> Here is the template to follow:
>
> [paste contents of `sad-template.md`]
>
> Here are the frozen upstream artifacts:
>
> **PRD:**
> [paste frozen PRD]
>
> **ACF:**
> [paste frozen ACF]
>
> Generate the SAD following the template exactly.

The AI will produce a SAD that follows the template structure, traces to the PRD, and respects ACF guardrails.

### Tips for Generation

- **One artifact per session.** Don't generate a PRD and SAD in the same session. Context bleeds and quality drops.
- **Paste the full upstream artifacts.** Don't summarize — the AI needs the details to trace requirements correctly.
- **Include the template.** Without it, the AI may invent its own structure, making validation harder.
- **Don't prompt the AI to be creative.** The prompts are designed to constrain output. Let them work.

---

## Validating an Artifact

Start a **new** AI session (not the one that generated the artifact) and provide:

1. **The validator** — The `{type}-validator.md` file
2. **The artifact to validate** — The generated artifact

### Example: Validating a SAD

> Here is the validator for a System Architecture Design:
>
> [paste contents of `sad-validator.md`]
>
> Here is the SAD to validate:
>
> [paste the generated SAD]

The AI will return structured JSON:

```json
{
  "status": "PASS | FAIL",
  "hard_gates": { ... },
  "blocking_issues": [ ... ],
  "warnings": [ ... ],
  "completeness_score": 85
}
```

### Why a Separate Session?

If the same AI that generated the artifact also validates it, it has a bias toward passing its own work. A fresh session with only the validator rules and the artifact produces a more honest evaluation.

### Handling FAIL Results

1. Review the `blocking_issues` — each one identifies a specific gate, description, and location
2. Fix only the blocking issues in the artifact (do not redesign)
3. Re-run the validator in a new session
4. If the same gate keeps failing after two cycles, the root cause may be in an upstream artifact — see the Re-entry Protocol in the playbook

---

## Inputs by Artifact Type

| Artifact | Prompt | Inputs |
|----------|--------|--------|
| PRD | `prd-prompt.md` | Product brief (human-written) |
| ACF | `acf-prompt.md` | Organizational context (human-written or existing) |
| SAD | `sad-prompt.md` | Frozen PRD + Frozen ACF |
| DCF | `dcf-prompt.md` | Organizational context (human-written or existing) |
| TDD | `tdd-prompt.md` | Frozen SAD + Frozen DCF |
| WDD | `wdd-prompt.md` | Frozen TDD |
| ORD | `ord-prompt.md` | Frozen TDD + Frozen ACF + Frozen DCF |

For validation, the input is always: the validator + the artifact being validated.

The DoR Validator (`dor-validator.md`) is run per WDD work item, not against the full WDD.

---

## The Execution Loop

Once WDD work items pass the DoR Validator, they enter the execution loop. Each phase uses its own prompt:

| Phase | Prompt | What You Provide |
|-------|--------|-----------------|
| Tests | `test-prompt.md` | WDD work item + TDD testing strategy + DCF testing expectations |
| Plan | `plan-prompt.md` | Approved test specs + TDD interface contracts + relevant source code |
| Code | `code-prompt.md` | Approved plan + approved test specs + WDD work item + ACF guardrails + source code |
| Review | `review-prompt.md` | Completed code + test results + WDD acceptance criteria + ACF guardrails |

Each phase is a separate AI session. The human approves the output of each phase before the next one begins.

### Generating the Execution Plan

Before starting the execution loop, use `execution-plan-prompt.md` to produce an ordered execution plan from the frozen WDD. This sequences every work item through the four phases, respects work group order and dependencies, and identifies the specific upstream artifact sections needed as input for each phase.

> [paste contents of `execution-plan-prompt.md`]
>
> **Frozen WDD:**
> [paste frozen WDD]
>
> **Frozen TDD:**
> [paste frozen TDD]
>
> **Frozen ACF:**
> [paste frozen ACF]
>
> **Frozen DCF:**
> [paste frozen DCF]

The output is a checklist you follow through the execution loop — one work item at a time, one phase at a time.

---

## Practical Workflow Summary

```
Session 1:  prompt + template + inputs  →  generated artifact
Session 2:  validator + artifact        →  PASS/FAIL JSON
            (if FAIL: fix, then new Session 2)
Session 3:  human reviews, approves, freezes
            → move to next artifact
```

Repeat for each artifact in the flow: PRD → ACF → SAD → DCF → TDD → WDD → Execute → ORD.

---

## Common Mistakes

**Dumping everything into one session.** The AI loses focus. Keep sessions artifact-scoped.

**Skipping the template.** Without a template, the AI structures the artifact however it wants, and the validator flags structural issues.

**Validating in the same session that generated.** The AI is biased toward its own output. Use a fresh session.

**Fixing warnings during validation.** Warnings are non-blocking. Fix only blocking issues unless a warning reveals a real problem.

**Summarizing upstream artifacts.** Paste the full frozen artifact. Summaries lose the detail that validators check for traceability.

**Letting the AI run the full flow autonomously.** The human is the orchestrator. The AI generates and validates one artifact at a time. Human judgment is the final gate at every step.
