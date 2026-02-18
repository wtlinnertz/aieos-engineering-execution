# DCF Template (Design Context File)

The DCF defines **design standards and expectations** that constrain TDD creation.
It is reusable across many initiatives.

## 0. Document Control
- DCF ID: DCF-CLI-001
- Owner: Project Maintainer
- Date: 2026-02-17
- Status: Frozen
- Applies To: ai-sdlc CLI

## 1. Purpose
Enforce design-level standards for the ai-sdlc CLI: design principles, quality bars, testing expectations, operational expectations, and documentation requirements that all TDDs must follow.

## 2. Design Principles (Hard)
- Principle 1: Prefer deterministic designs over exploratory steps — each component has a clear, predictable input-output contract.
- Principle 2: Design for failure — every external call (LLM API, file I/O) must have defined failure behavior. No silent failures.
- Principle 3: Make interfaces explicit — component boundaries, function signatures, and data shapes must be defined, not implied.
- Principle 4: Prefer smallest safe change — designs should not introduce unnecessary abstraction or generalization beyond current requirements.
- Principle 5: No side effects on failure — a failed generation or validation must not corrupt or modify existing artifact files on disk.
- Principle 6: Single responsibility per module — each module handles one concern (CLI parsing, workflow logic, prompt assembly, LLM communication, file operations).

## 3. Quality Bars (Hard)
- Interfaces and contracts must be explicit — TypeScript types/interfaces for all component boundaries.
- Failure and rollback behavior must be defined for every operation that touches the filesystem or LLM API.
- Tests must be defined with pass/fail criteria for each component.
- Operability must be documented — CLI output for success, failure, and status must be specified.
- Quality bar 1: All public functions must have TypeScript type signatures (strict mode enforced).
- Quality bar 2: No `any` types in component interfaces — explicit types required at all boundaries.
- Quality bar 3: Error messages must identify the failure source (LLM API, file system, workflow rule) and provide actionable guidance.

## 4. Non-Goals Enforcement (Hard)
- TDD must explicitly restate non-goals from SAD.
- TDD must not implement excluded functionality.
- "Helpful" scope expansion is not allowed.
- TDD must not introduce web UI, GUI, daemon, agent framework, database, telemetry, or multi-provider support beyond the provider-swappable interface.
- TDD must not add runtime dependencies beyond `@anthropic-ai/sdk` and `commander`.

## 5. Operational Expectations (Hard)
- Deployment verification expectations: After installation (`npm install -g ai-sdlc`), running `sdlc status` in a project directory must produce meaningful output confirming the CLI is functional and the kit files are accessible.
- Monitoring/alerting expectations (what must be observable): CLI must output clear success/failure status for every command. Validation results must include the full structured JSON (PASS/FAIL, blocking issues, warnings). LLM API errors must be reported with the error type (timeout, rate limit, auth failure).
- Auditability expectations: Each generated artifact includes a Document Control section with traceability to upstream artifacts. Validation results are saved as JSON files alongside artifacts.

## 6. Testing Expectations (Hard)
- Required test layers:
  - Unit tests: individual component logic (Workflow Engine rules, Prompt Assembler file reading, File Manager state tracking)
  - Integration tests: command handler flows with mocked LLM responses (generate, validate, freeze, status)
  - No end-to-end tests against live LLM API required for v1 (cost and non-determinism)
- Evidence requirements (logs, reports, artifacts): Vitest test reports with pass/fail counts. Test coverage report for core modules.
- Promotion gates (what blocks progression): All unit and integration tests must pass. No TypeScript compilation errors in strict mode.

### Evidence Management
- Required evidence formats: Vitest test output (console or JSON reporter), TypeScript compilation output (zero errors).
- Evidence storage location: Test results stored locally; CI integration is out of scope per PRD non-goals.
- Retention requirements: Not applicable — local CLI tool. Test results are ephemeral unless the user captures them.
- Accessibility requirements: Not applicable — single-user local tool with no audit retrieval requirements.

## 7. Documentation Expectations (Hard)
- Required sections in a TDD: All sections from the TDD template must be present and substantive. Intent verification, scope restatement, component designs, interface contracts, error handling, test specifications.
- Required diagram types (if any): Component interaction diagrams in Mermaid where multiple components interact. Sequence diagrams for key flows (generate, validate) in Mermaid.
- Required traceability markers: Each TDD must reference SAD-CLI-001, PRD-CLI-001, and ACF-CLI-001. Each design decision must trace to a SAD component or deferred decision.

## 8. Open Items
None.

## 9. Freeze Declaration (when ready)
This DCF is approved and frozen. All TDDs must comply.

- Approved By: Project Maintainer
- Date: 2026-02-17
