# CLAUDE.md — AI Operating Instructions for aieos-engineering-execution

## Project Overview

**aieos-engineering-execution** is a structured AI-assisted SDLC documentation and governance framework. It provides artifact templates, generation prompts, validators, and a playbook for moving from product intent to execution-ready work.

This is a **documentation and process** project, not a code project. All artifacts are Markdown.

## Repository Structure

```
docs/
  principles/      # Organizational engineering and product standards (ACF/DCF input)
  specs/           # Authoritative content rules and quality criteria per artifact
  artifacts/       # Templates and intake forms for each SDLC artifact
  prompts/         # Canonical AI generation prompts (behavior only)
  validators/      # Strict quality gate definitions (judgment only)
  governance-model.md    # AIEOS structural rules, taxonomy, and invariants
  playbook.md            # End-to-end process definition
  index.md               # Documentation entry point
  how-to-adapt.md        # Organizational adoption guidance
  how-to-use-with-ai.md  # Artifact-by-artifact AI usage guide

examples/
  end-to-end/
    example-01-generic-service/   # Complete worked example

tests/
  kit-test-plan.md  # Structural and flow scenario test plan
```

## Artifact Flow (Non-Negotiable Order)

1. Product Brief (intake form) → PRD → PRD Validator → Freeze
2. Architecture Context (intake form) → ACF → ACF Validator → Freeze
3. PRD + ACF → SAD → SAD Validator → Freeze
4. Design Context (intake form) → DCF → DCF Validator → Freeze
5. SAD + DCF → TDD → TDD Validator → Freeze
6. TDD → WDD → WDD Validator → DoR Validator → Consistency Check → Human Approval → Freeze
7. WDD (frozen) → Execution Plan (`execution-plan-prompt.md`) → Human Approval
8. Execute per work item in plan order (Tests → Plan → Code → Review)
9. Execute → ORD → ORD Validator → Production Ready

Prerequisite documents must exist and be frozen before downstream artifacts are generated.

**Brownfield projects**: Run `codebase-analysis-prompt.md` first to pre-fill intake forms for ACF, DCF, and SAD.

**Re-entry / changes to frozen artifacts**: Use `impact-analysis-prompt.md` to assess downstream effects before modifying any frozen artifact.

## Naming Conventions

- Specs: `{type}-spec.md` (e.g., `prd-spec.md`) — in `docs/specs/`
- Templates: `{type}-template.md` (e.g., `prd-template.md`) — in `docs/artifacts/`
- Intake forms: `{context}-template.md` (e.g., `architecture-context-template.md`) — in `docs/artifacts/`
- Prompts: `{type}-prompt.md` (e.g., `prd-prompt.md`) — in `docs/prompts/`
- Validators: `{type}-validator.md` (e.g., `prd-validator.md`) — in `docs/validators/`
- Example artifacts: `{nn}-{type}.md` (e.g., `01-prd.md`, `02-acf.md`)
- Artifact IDs: `{TYPE}-{PROJECT}-{NNN}` (e.g., `PRD-EX-001`)
- Execution plan context files: `{nn}-{wdd-item-id}-context.md` (e.g., `09-WDD-EX-001-context.md`)
- Execution phase outputs: `{nn}-{wdd-item-id}-{phase}.md` where phase is `tests`, `plan`, or `review` (e.g., `09-WDD-EX-001-tests.md`)
- Work group gate files: `{nn}-wg-{n}-gate.md` (e.g., `16-wg-05-gate.md`)

## Validator Output Format (Standardized)

All validators produce JSON with this schema:

```json
{
  "status": "PASS | FAIL",
  "summary": "<one sentence verdict>",
  "hard_gates": { "<gate_name>": "PASS | FAIL" },
  "blocking_issues": [{ "gate": "", "description": "", "location": "" }],
  "warnings": [{ "description": "", "location": "" }],
  "completeness_score": "<0-100>"
}
```

## Operating Rules

When generating artifacts:
- Output pure Markdown
- Use the template exactly as written; do not add or remove sections
- Satisfy all content rules, format requirements, and hard gates defined in the corresponding spec
- Do not infer missing information; mark it explicitly
- Do not expand scope beyond upstream artifacts
- Non-goals are enforceable; do not violate them

When validating artifacts:
- Evaluate against the hard gates and content rules defined in the corresponding spec
- Be strict; ambiguity is a failure condition
- Do not redesign or suggest solutions
- Evaluate only what is explicitly present
- Any hard gate failure means FAIL

## Key Design Decisions

- **Four-file system**: Each artifact type has four files — spec (content rules), template (structure), prompt (AI behavior), validator (judgment). Each file answers exactly one question.
- **Tool-agnostic**: No references to specific work management tools (Jira, etc.) or AI providers.
- **Greenfield and brownfield**: Supports both new projects and existing codebases via intake forms and codebase analysis.
- **Markdown as system of record**: All artifacts are Markdown, human and machine readable.
- **Validators per document**: Every artifact type has a corresponding validator.
- **Specs as single source of truth**: Hard gates, content rules, and quality criteria are defined in specs. Prompts and validators reference specs, not inline their own rules.
- **Intent verification**: Built into SAD, TDD, and WDD generation prompts as a self-check. Validators enforce intent integrity as a hard gate.

## Commit Message Style

Follow conventional commits: `docs: <description>`

Examples from history:
- `docs: enhance SAD validator with comprehensive validation rules and criteria`
- `docs: add WDD generation prompt`
- `docs: align SAD template with validator requirements`

## What Not To Do

- Do not introduce new document types without explicit request
- Do not merge or combine artifact types
- Do not make validators "helpful" (no suggestions, no redesign)
- Do not add tool-specific references
- Do not skip the artifact dependency order
- Do not silently modify existing constraints
