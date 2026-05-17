# aieos-engineering-execution

AI-assisted templates, validators, and workflows for the software development lifecycle.

## Overview

**aieos-engineering-execution** is a practical, open toolkit for teams that want to use AI safely and effectively across SDLC.

It provides:
- Lean, AI-first artifact templates
- Strict, non-prescriptive validators
- A repeatable promotion and freeze model
- Examples that show how everything fits together

This repository focuses on process and artifacts, not tools or implementations.

## The problem

AI generates content well. But without structure and gates, you get:
- Ambiguity
- Scope creep
- Overdesign
- Unexecutable work
- Fragile delivery

This kit enforces:
- Clear intent before generation
- Explicit scope and non-goals
- Deterministic handoffs between stages
- Hard validation gates before execution

The result: predictable delivery, even with AI in the loop.

## Who this is for

- Software engineers
- Platform and DevOps engineers
- Architects
- Technical leads
- Teams experimenting with AI-assisted delivery
- Organizations that need governance without bureaucracy

You don’t need a specific toolchain to use this kit.

## What’s included

**Artifact templates** - PRD, ACF, SAD, DCF, TDD, WDD, ORD. Also DKR (Domain Knowledge Record — optional, documents domain model before SAD/TDD when domain complexity warrants it). Each template is designed to be passed directly to an AI, easy to validate, and hard to misuse.

**Validators** - PRD, ACF, SAD, DCF, TDD, WDD, Definition of Ready, and ORD validators. They’re strict and non-prescriptive: they determine readiness without redesigning or suggesting solutions.

**Playbook** - End-to-end guidance covering artifact flow, promotion rules, refinement ladders, freeze points, intent verification checkpoints, execution loops (Tests → Plan → Code → Review), and completion criteria.

**Examples** - Anonymized, end-to-end examples showing how artifacts evolve from PRD to execution, how validators pass or fail, and what good looks like in practice.

## Core principles

- AI generates, validators decide
- Artifacts are promoted, not rewritten
- Execution artifacts are stricter than design artifacts
- Ambiguity is a failure condition
- Non-goals are enforceable

## Getting started

1. Fill out a Product Brief to capture intent, scope, and context
2. Generate a PRD using the provided prompt and template
3. For each downstream artifact: generate using prompt and template (intent verification is built in)
4. Run the corresponding validator
5. Fix blocking issues only
6. Freeze and promote the artifact
7. Repeat until you reach execution-ready WDD work items

See `docs/index.md` to get started.

## Documentation

- **Start here** `docs/index.md`
- **Playbook** `docs/playbook.md`
- **Templates** `docs/artifacts/`
- **Validators** `docs/validators/`
- **Examples** `examples/`

## Spec-driven CI/CD (M5.1)

The kit's Layer-4 outputs now include a frozen **CI spec** that downstream
execution consumes. The CI spec is authored AFTER the ORD freezes and
captures tool-agnostic success criteria for every pipeline action.

- **Template** `templates/ci-spec/python-k8s-flux.yaml` — starting point
  for a Python web service deployed to Kubernetes via Flux. Pre-populated
  with the ten v1 adapters and realistic default thresholds.
- **Authoring prompt** `prompts/ci-spec-author.md` — step-by-step
  interview that walks the developer through overrides instead of handing
  them a raw YAML to guess at.
- **Schema** — CI specs validate against
  `aieos-governance-foundation/schema/ci-spec.schema.json` frozen at
  `v1.0-ci-spec-schema`.
- **Freeze** — the authored spec is committed to the target repo at
  `.aieos/ci.spec.yaml` and cached in the artifact store. The pipeline
  runner (`aieos-pipeline-runner`) refuses unfrozen specs at execution
  time, so freeze-before-promote is enforced by the tool.

## Anonymization & Safety

This is a public repository. All content is employer-neutral, uses placeholders instead of real identifiers, and avoids internal systems, URLs, and names. See `ANONYMIZATION.md` before contributing.

## Contributing

Contributions are welcome. Please read:
- `CONTRIBUTING.md`
- `TERMS.md`
- `ANONYMIZATION.md`

Small, focused improvements are preferred.

## License

MIT License.

## About the design

This kit is intentionally disciplined. If something feels heavy, it’s usually because it prevents downstream chaos. If something feels light, it’s because it’s meant to be reused safely.

