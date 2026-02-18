# Product Requirements Document — ai-sdlc CLI

## 0. Document Control
- Product / Initiative Name: ai-sdlc CLI
- PRD ID: PRD-CLI-001
- Author: AI-generated from Product Brief
- Date: 2026-02-17
- Status: Frozen
- Related Links:
  - `docs/playbook.md`
  - `docs/how-to-use-with-ai.md`
  - `docs/how-to-adapt.md`

## 1. Problem Statement
- **What problem are we solving?** Using the ai-sdlc-kit playbook today requires manually copying prompt files, pasting frozen upstream artifacts, managing separate AI sessions for generation and validation, remembering artifact flow order and prerequisite rules, manually interpreting validation results and feeding them back into regeneration, and informally tracking freeze state with no mechanical enforcement. This manual process is error-prone (wrong inputs, skipped validators, forgotten prerequisites) and discourages adoption.
- **Who experiences this problem?** Developers and tech leads using ai-sdlc-kit to plan and execute features. Teams evaluating the kit.
- **Why now?** The prompts, templates, and validators already exist. The missing piece is orchestration that makes the playbook executable rather than just readable.

## 2. Goals (What "Success" Means)
- Goal 1: A user can take a product brief from zero to a frozen WDD (and through to ORD after execution) using only the CLI, with no manual prompt assembly.
- Goal 2: Validation results from the CLI match what you'd get from manually running the validator prompts.
- Goal 3: Freeze state is enforced — the user cannot skip steps or generate artifacts out of order.

## 3. Non-Goals (Hard Exclusions)
- Non-goal 1: No web UI or GUI — CLI only.
- Non-goal 2: No multi-user collaboration features — single user drives the workflow.
- Non-goal 3: No built-in CI/CD integration — the CLI is a local tool; CI/CD wraps it externally if desired.
- Non-goal 4: No agent framework (LangGraph, AutoGen, CrewAI) — thin orchestration over API calls.
- Non-goal 5: No persistent server or daemon — stateless CLI, state is files on disk.
- Non-goal 6: No modification to the kit's prompts, templates, or validators — the CLI consumes them as-is.

## 4. Users / Personas
- Persona 1: Developers and tech leads using ai-sdlc-kit
  - Primary needs: A tool that handles the mechanics of the playbook so they can focus on decisions, not prompt assembly.
  - Constraints: Must have LLM API access and credentials.
- Persona 2: Teams evaluating the kit
  - Primary needs: A working CLI that makes the playbook tangible and lowers the barrier to trying it.
  - Constraints: Not provided.

## 5. Requirements

### 5.1 Functional Requirements
- FR-1: The system SHALL generate any artifact type by reading the corresponding prompt, template, and frozen upstream artifacts from disk, assembling them into a single LLM call, and writing the output to the project's `docs/sdlc/` directory.
- FR-2: The system SHALL validate any artifact by reading the corresponding validator and the artifact, calling the LLM, and returning structured JSON (PASS/FAIL).
- FR-3: The system SHALL enforce the artifact flow order by refusing to generate an artifact if its prerequisites are not frozen.
- FR-4: The system SHALL track freeze state per artifact (which artifacts exist, which are frozen).
- FR-5: The system SHALL support the validation loop: on FAIL, show blocking issues and allow regeneration.
- FR-6: The system SHALL support human approval gates: after validator PASS, prompt the user to approve before freezing.
- FR-7: The system SHALL show workflow status: which artifacts exist, which are frozen, and what step is next.
- FR-8: The system SHALL support the execution loop phases (test, plan, code, review) for individual WDD work items.
- FR-9: The system SHALL read kit files (prompts, templates, validators) from a configurable kit location (local path or standard install location).
- FR-10: The system SHALL read/write project artifacts to `docs/sdlc/` in the current project.
- FR-11: The system SHALL call a single LLM API for generation and validation, with a provider-swappable design.
- FR-12: The system SHALL use the invocation pattern `sdlc <command> <artifact-type>` (verb-noun), with core commands: `generate`, `validate`, `freeze`, `status`.

### 5.2 Non-Functional Requirements
- NFR-1: The CLI SHALL respond within the latency of the underlying LLM API call, with no added overhead beyond I/O.
- NFR-2: The CLI SHALL work on macOS and Linux.
- NFR-3: The CLI SHALL handle LLM API errors gracefully (timeouts, rate limits, auth failures).
- NFR-4: The CLI SHALL NOT store secrets — API keys come from environment variables or config files excluded from version control.
- NFR-5: The CLI SHALL NOT collect telemetry or data.
- NFR-6: The CLI SHALL be distributed as an npm package (installable via `npm install -g ai-sdlc`), with cloning the repo and running directly also acceptable for v1.
- NFR-7: The CLI SHALL have no complex build or native dependencies.

## 6. Constraints (Hard Guardrails)
- Constraint 1: CLI only — no web UI, GUI, or daemon.
- Constraint 2: No agent frameworks — thin orchestration over direct API calls.
- Constraint 3: Markdown-based artifacts only — no new file formats.
- Constraint 4: The CLI consumes kit prompts, templates, and validators as-is — no modification.
- Constraint 5: Stateless CLI — all state is files on disk.
- Constraint 6: v1 supports Anthropic Claude only. Design is provider-swappable for future additions.

## 7. Assumptions
- Assumption 1: The user has a working LLM API key configured.
- Assumption 2: The ai-sdlc-kit files are accessible on disk.
- Assumption 3: The project uses the `docs/sdlc/` convention for artifact storage.

## 8. Out of Scope by Default
Anything not explicitly included in Sections 2 and 5 is out of scope unless added via PRD change.

## 9. Open Questions
None. All previously open questions (CLI command structure, installation method, LLM provider scope) have been resolved in the updated Product Brief.

## 10. Acceptance / Success Criteria
- Success criterion 1: A user can take a product brief from zero to a frozen WDD (and through to ORD after execution) using only the CLI, with no manual prompt assembly.
- Success criterion 2: Validation results match what you'd get from manually running the validator prompts.
- Success criterion 3: Freeze state is enforced — the user cannot skip steps or generate artifacts out of order.
- Success criterion 4: The CLI can execute the full artifact flow: Product Brief → PRD → ACF → SAD → DCF → TDD → WDD → ORD, with validation and freeze at each step.
- Success criterion 5: ORD generation and validation work the same as other artifacts; the user gathers deployment evidence manually before running the CLI.
- Success criterion 6: The CLI can execute the validation loop (FAIL → show issues → regenerate → re-validate).
- Success criterion 7: The CLI works with Anthropic Claude as the LLM provider.
- Success criterion 8: Documentation exists for installation and basic usage.

## 11. Freeze Declaration (when ready)
This PRD is approved and frozen. Downstream artifacts may not reinterpret or expand intent.

- Approved By: Project Maintainer
- Date: 2026-02-17
