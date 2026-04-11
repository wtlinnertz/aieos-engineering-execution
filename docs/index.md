# aieos-engineering-execution Documentation

This toolkit helps you use AI safely across the software development lifecycle (SDLC). The documentation explains how to use the kit, why it works, and how the pieces fit together.

## Start here

Follow this order if you’re new to the kit:

1. **Understand the process**: Read the Playbook to learn the end-to-end flow, gates, and freeze points.
2. **Use the templates**: Generate SDLC artifacts using the AI-first templates.
3. **Apply validators**: Run validators to determine readiness before promotion.
4. **Learn by example**: Walk through the anonymized, end-to-end examples.

## Core concepts

The kit is built on a few simple ideas:

- AI generates, validators decide
- Artifacts are promoted, not rewritten
- Execution artifacts are stricter than design artifacts
- Ambiguity is a failure condition
- Non-goals are enforceable

These principles apply consistently across all artifacts and validators.

## Documentation sections

**Governance model** - Defines the structural rules, taxonomy, and invariants for the AIEOS kit system. Covers the four-file system (spec, template, prompt, validator), repository structure, artifact promotion model, inter-kit handoff contracts, and adoption guidance.
See: `governance-model.md`

**Playbook** - The canonical process from product intent to delivered work. Covers artifact flow and promotion rules, refinement ladders, intent verification, the execution loop (Tests → Plan → Code → Review), and completion criteria.
See: `playbook.md`

**Engineering and product principles** - Organizational standards for ACF and DCF creation. Covers code quality, testing, architecture, security, problem diagnosis, outcomes, scope, and traceability.
See: `principles/`

**Artifact specifications** - Authoritative content rules and quality criteria for each artifact. Define what makes an artifact good (content rules, format requirements, hard gates). These are the single source of truth for quality criteria referenced by prompts and validators.
See: `specs/`

**Artifact templates** - Lean, AI-first templates for each SDLC stage. Includes PRD, ACF, SAD, DCF, TDD, WDD, and ORD. Templates are structure only, designed to be passed directly to AI for consistent output and easy validation.
See: `artifacts/`

**Utility prompts** - Non-governed analytical supplements including codebase-analysis (brownfield system analysis for ACF/DCF/SAD intake), impact-analysis (downstream impact assessment), and dprd-consistency-check (cross-boundary validation).
See: `prompts/`

**Validators** - Strict, non-prescriptive readiness gates including PRD, ACF, SAD, DCF, TDD, WDD, DoR, Consistency, Impact Analysis, and ORD validators. Validators assess readiness, not correctness or implementation quality.
See: `validators/`

**Examples** - Anonymized examples showing artifact evolution from PRD to execution, PASS/FAIL validator outputs, and working reference implementations.
See: `../examples/`

## How to use this kit with AI

Generate and validate artifacts in separate AI sessions:

1. **Generate**: Provide the AI with the spec, prompt, template, and frozen upstream artifacts
2. **Validate**: In a new session, provide the spec, validator, and the generated artifact
3. **Freeze**: Once PASS and human-approved, lock the artifact and move on

The human orchestrates the flow. The AI generates and validates one artifact at a time. For detailed guidance, session examples, and common failure modes, see `how-to-use-with-ai.md`.

## Anonymization and safety

This is a public repository. All examples and templates avoid real company names, use placeholders for identifiers, and remain tool- and employer-neutral.

Before contributing, read `ANONYMIZATION.md` and `TERMS.md`.

## Contributing

Contributions are welcome. See `CONTRIBUTING.md`, `CODE_OF_CONDUCT.md`, and `SECURITY.md`. Small, focused improvements are preferred.

## Guides

- `session-setup.md`: Per-artifact setup checklists, pre-flight gate checks, and common failure reminders
- `troubleshooting.md`: Gate failure remediation guide
- `entry-from-pik.md`: Boundary briefing when arriving from the Product Intelligence Kit (Path A)

## Where to go next

- Read the Playbook → `playbook.md`
- Review Specifications → `specs/`
- Browse Templates → `artifacts/`
- Review Validators → `validators/`
- Explore Examples → `../examples/`

This kit is intentionally disciplined. The structure exists to make AI-assisted delivery predictable, safe, and repeatable.
