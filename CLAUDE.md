# CLAUDE.md — AI Operating Instructions for ai-sdlc-kit

## Project Overview

**ai-sdlc-kit** is a structured AI-assisted SDLC documentation and governance framework. It provides artifact templates, generation prompts, validators, and a playbook for moving from product intent to execution-ready work.

This is a **documentation and process** project, not a code project. All artifacts are Markdown.

## Repository Structure

```
docs/
  artifacts/       # Lean, AI-first templates for each SDLC artifact
  prompts/         # Canonical AI generation prompts
  validators/      # Strict quality gate definitions
  playbook.md      # End-to-end process definition
  index.md         # Documentation entry point
  how-to-adapt.md  # Organizational adoption guidance

examples/
  end-to-end/
    example-01-generic-service/   # Complete worked example

TERMS.md           # Glossary of project terminology
```

## Artifact Flow (Non-Negotiable Order)

1. PRD → PRD Validator → Freeze
2. ACF → ACF Validator → Freeze
3. PRD + ACF → SAD → SAD Validator → Freeze
4. DCF → DCF Validator → Freeze
5. SAD + DCF → TDD → TDD Validator → Freeze
6. TDD → WDD → WDD Validator → Freeze
7. WDD → Stories → DoR Validator → Execute

Prerequisite documents must exist and be frozen before downstream artifacts are generated.

## Naming Conventions

- Templates: `{type}-template.md` (e.g., `prd-template.md`)
- Prompts: `{type}-prompt.md` (e.g., `prd-prompt.md`)
- Validators: `{type}-validator.md` (e.g., `prd-validator.md`)
- Example artifacts: `{nn}-{type}.md` (e.g., `01-prd.md`, `02-acf.md`)
- Artifact IDs: `{TYPE}-{PROJECT}-{NNN}` (e.g., `PRD-EX-001`)

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
- Do not infer missing information; mark it explicitly
- Do not expand scope beyond upstream artifacts
- Non-goals are enforceable; do not violate them

When validating artifacts:
- Be strict; ambiguity is a failure condition
- Do not redesign or suggest solutions
- Evaluate only what is explicitly present
- Any hard gate failure means FAIL

## Key Design Decisions

- **Tool-agnostic**: No references to specific work management tools (Jira, etc.). Use "story" not "Jira story".
- **Markdown as system of record**: All artifacts are Markdown, human and machine readable.
- **Validators per document**: Every artifact type has a corresponding validator.
- **Intent Summary pre-pass**: Required before generating SAD, TDD, or WDD. Not persisted.

## Commit Message Style

Follow conventional commits: `docs: <description>`

Examples from history:
- `docs: enhance SAD validator with comprehensive validation rules and criteria`
- `docs: add story prompt`
- `docs: add WDD generation prompt`

## What Not To Do

- Do not introduce new document types without explicit request
- Do not merge or combine artifact types
- Do not make validators "helpful" (no suggestions, no redesign)
- Do not add tool-specific references
- Do not skip the artifact dependency order
- Do not silently modify existing constraints
