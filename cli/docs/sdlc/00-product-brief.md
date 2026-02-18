# Product Brief — ai-sdlc CLI

## Why

### Objective
- Build a command-line tool that automates the ai-sdlc-kit playbook workflow
- Reduce the manual effort of assembling prompts, inputs, and running LLM calls for each artifact step
- Make the playbook executable, not just readable

### Current Problem
- Today, using the kit requires manually copying prompt files, pasting frozen upstream artifacts, and managing separate AI sessions for generation and validation
- Users must remember the artifact flow order, which inputs each step needs, and prerequisite rules
- Validation results must be manually interpreted and fed back into regeneration
- Freeze state is informal — there's no mechanical enforcement of "this artifact is frozen"
- The manual process is error-prone: wrong inputs, skipped validators, forgotten prerequisites
- This friction discourages adoption and makes the playbook feel heavier than it is

---

## What

### Functional Requirements
- Generate any artifact type by reading the corresponding prompt, template, and frozen upstream artifacts from disk, assembling them into a single LLM call, and writing the output to the project's `docs/sdlc/` directory
- Validate any artifact by reading the corresponding validator and the artifact, calling the LLM, and returning structured JSON (PASS/FAIL)
- Enforce the artifact flow order: refuse to generate an artifact if its prerequisites are not frozen
- Track freeze state per artifact (which artifacts exist, which are frozen)
- Support the validation loop: on FAIL, show blocking issues and allow regeneration
- Support human approval gates: after validator PASS, prompt the user to approve before freezing
- Show workflow status: which artifacts exist, which are frozen, what step is next
- Support the execution loop phases (test, plan, code, review) for individual WDD work items

### CLI Command Structure
- Invocation pattern: `sdlc <command> <artifact-type>` (verb-noun)
- Core commands: `generate`, `validate`, `freeze`, `status`
- Examples: `sdlc generate prd`, `sdlc validate sad`, `sdlc freeze tdd`, `sdlc status`

### Installation and Distribution
- Distributed as an npm package (installable via `npm install -g ai-sdlc`)
- For v1, cloning the repo and running directly is also acceptable
- No complex build or native dependencies

### LLM Provider
- v1 supports Anthropic Claude only
- Design should be provider-swappable so others can be added later
- Only Anthropic needs to work at launch

### Scope
- CLI tool invoked from the project root directory
- Reads kit files (prompts, templates, validators) from a configurable kit location (local path or standard install location)
- Reads/writes project artifacts to `docs/sdlc/` in the current project
- Calls a single LLM API for generation and validation (provider-swappable)
- Works with the existing markdown-based artifacts — no new file formats

### Exclusions
- No web UI or GUI — CLI only
- No multi-user collaboration features — single user drives the workflow
- No built-in CI/CD integration — the CLI is a local tool; CI/CD wraps it externally if desired
- No agent framework (LangGraph, AutoGen, CrewAI) — thin orchestration over API calls
- No persistent server or daemon — stateless CLI, state is files on disk
- No modification to the kit's prompts, templates, or validators — the CLI consumes them as-is

### Reference Documents
- `docs/playbook.md` — the process being automated
- `docs/how-to-use-with-ai.md` — the manual workflow being replaced
- `docs/how-to-adapt.md` — project setup conventions (`docs/sdlc/`)

---

## Who

### Target Personas
- **Primary:** Developers and tech leads using ai-sdlc-kit to plan and execute features. They understand the playbook conceptually but want a tool that handles the mechanics.
- **Secondary:** Teams evaluating the kit. A working CLI makes the playbook tangible and lowers the barrier to trying it.

### External Dependencies
- An LLM API provider (Anthropic, OpenAI, or similar) — the user must have API access and credentials
- The ai-sdlc-kit repository — the CLI reads prompts, templates, and validators from it

### Sponsor
- Project maintainer

### Blockers
- None identified. The prompts, templates, and validators already exist. The CLI is orchestration only.

---

## When

### Release Criteria
- CLI can execute the full artifact flow: Product Brief → PRD → ACF → SAD → DCF → TDD → WDD → ORD (with validation and freeze at each step)
- ORD generation and validation work the same as other artifacts; the user gathers deployment evidence manually before running the CLI
- CLI can execute the validation loop (FAIL → show issues → regenerate → re-validate)
- CLI enforces prerequisite order and freeze state
- CLI works with at least one LLM provider (Anthropic Claude)
- Documentation exists for installation and basic usage

### Success Criteria
- A user can take a product brief from zero to a frozen WDD (and through to ORD after execution) using only the CLI (no manual prompt assembly)
- Validation results match what you'd get from manually running the validator prompts
- Freeze state is enforced — you cannot skip steps or generate out of order

### Timeline
- Not specified — quality over speed

---

## How (Non-Functional)

### Non-Functional Requirements
- CLI should respond within the latency of the underlying LLM API call (no added overhead beyond I/O)
- Must work on macOS and Linux
- Must handle LLM API errors gracefully (timeouts, rate limits, auth failures)

### Assumptions
- The user has a working LLM API key configured
- The ai-sdlc-kit files are accessible on disk
- Project uses the `docs/sdlc/` convention for artifact storage

### Risks
- LLM output quality varies — the CLI can't guarantee a PASS on first generation. Mitigation: the validation loop exists for this reason.
- Prompt token limits — large upstream artifacts may exceed context windows. Mitigation: the kit's artifacts are designed to be concise; warn the user if input exceeds a threshold.

### Compliance
- No secrets stored by the CLI — API keys come from environment variables or config files excluded from version control
- No telemetry or data collection

---

## Completeness Checklist

- [x] Problem is clearly stated
- [x] At least one goal or outcome is defined
- [x] Scope boundaries are explicit (in scope and out of scope)
- [x] Primary users or personas are identified
- [x] Known constraints are listed
