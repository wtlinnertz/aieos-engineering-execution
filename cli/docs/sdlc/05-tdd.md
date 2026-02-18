# TDD Template (Technical Design Document)

## 0. Document Control
- System / Component Name: ai-sdlc CLI
- TDD ID: TDD-CLI-001
- Author: AI-generated from SAD-CLI-001 + DCF-CLI-001
- Date: 2026-02-17
- Status: Frozen
- Upstream Artifacts:
  - SAD ID / Link: SAD-CLI-001
  - ACF ID / Link: ACF-CLI-001
  - DCF ID / Link: DCF-CLI-001
- Related ADRs: None

## 1. Intent Summary
- The CLI automates the ai-sdlc-kit playbook workflow, replacing manual prompt assembly, LLM session management, and informal freeze tracking.
- Users are developers and tech leads who understand the playbook but want a tool that handles the mechanics.
- The system covers the full artifact flow: Product Brief → PRD → ACF → SAD → DCF → TDD → WDD → ORD, with validation and freeze at each step.
- Freeze state is enforced — artifacts cannot be generated out of order or without frozen prerequisites.
- The validation loop (FAIL → show issues → regenerate → re-validate) is a core workflow.
- Human approval gates exist: after validator PASS, the user approves before freezing.
- No web UI, GUI, or daemon — CLI only.
- No agent frameworks — thin orchestration over direct LLM API calls.
- No modification to kit prompts, templates, or validators — consume as-is.
- Stateless CLI — all state is files on disk.
- v1 supports Anthropic Claude only; design is provider-swappable.
- No telemetry or data collection.

## 2. Scope and Non-Goals (Hard Boundary)

### In Scope
- Generate artifacts by assembling prompt + template + frozen upstream artifacts into an LLM call
- Validate artifacts by assembling validator + artifact into an LLM call returning structured JSON
- Enforce artifact flow order and prerequisite freeze state
- Track artifact existence and freeze state via the `Status:` field in artifact Markdown files
- Support the validation loop (FAIL → show issues → regenerate)
- Support human approval gates before freezing
- Show workflow status (which artifacts exist, frozen, next step)
- Support execution loop phases (test, plan, code, review) for WDD work items
- CLI invocation pattern: `sdlc <command> <artifact-type>` with commands `generate`, `validate`, `freeze`, `status`

### Explicit Non-Goals (Must align with SAD)
- No web UI or GUI
- No multi-user collaboration features
- No built-in CI/CD integration
- No agent frameworks
- No persistent server or daemon
- No modification to kit files
- No database or external state store
- No runtime dependencies beyond `@anthropic-ai/sdk` and `commander`
- No telemetry or data collection
- No multi-provider support in v1 (only Anthropic Claude)

## 3. Technical Overview

### Components involved
Six modules corresponding to the SAD's major components:

1. **CLI Entry Point** (`src/cli.ts`) — commander-based argument parsing, routes to command handlers.
2. **Command Handlers** (`src/commands/generate.ts`, `validate.ts`, `freeze.ts`, `status.ts`) — one module per command, coordinates other components.
3. **Workflow Engine** (`src/workflow.ts`) — artifact flow definitions, prerequisite checking, state queries.
4. **Prompt Assembler** (`src/assembler.ts`) — reads kit files and upstream artifacts, concatenates into a prompt string.
5. **LLM Client** (`src/llm/client.ts` + `src/llm/anthropic.ts`) — provider interface + Anthropic implementation.
6. **File Manager** (`src/files.ts`) — reads/writes artifacts, reads kit files, parses freeze state from Markdown.

### Key flows

**Generate flow:** CLI parses `sdlc generate <type>` → Workflow Engine checks prerequisites are frozen → Prompt Assembler reads prompt + template + upstream artifacts → LLM Client sends assembled prompt → File Manager writes response to `docs/sdlc/{nn}-{type}.md` with `Status: Draft`.

**Validate flow:** CLI parses `sdlc validate <type>` → Prompt Assembler reads validator + artifact → LLM Client sends assembled prompt → parse JSON response → display PASS/FAIL result with details → File Manager writes validation JSON to `docs/sdlc/{nn}-{type}-validation.json`.

**Freeze flow:** CLI parses `sdlc freeze <type>` → Workflow Engine checks artifact exists and has passing validation → prompt user for confirmation → File Manager updates `Status: Draft` to `Status: Frozen` in the artifact file and writes freeze declaration fields.

**Status flow:** CLI parses `sdlc status` → File Manager scans `docs/sdlc/` for artifact files → Workflow Engine determines state of each artifact and next step → display table to user.

### Data movement (high level)
Kit files (read-only) → Prompt Assembler → assembled prompt string → LLM Client → Anthropic API → response text → File Manager → artifact files on disk (read-write).

## 4. Interfaces and Contracts (Hard)

### CLI Interface

- Name: CLI commands
- Inputs:
  - `sdlc generate <type>` — type is one of: `prd`, `acf`, `sad`, `dcf`, `tdd`, `wdd`, `ord`
  - `sdlc validate <type>` — same type options
  - `sdlc freeze <type>` — same type options
  - `sdlc status` — no additional arguments
- Outputs:
  - generate: writes artifact file to `docs/sdlc/{nn}-{type}.md`, prints confirmation to stdout
  - validate: writes validation JSON to `docs/sdlc/{nn}-{type}-validation.json`, prints PASS/FAIL summary to stdout
  - freeze: updates artifact file status field, prints confirmation to stdout
  - status: prints artifact status table to stdout
- Error modes: missing prerequisites (exit code 1, message listing missing artifacts), LLM API errors (exit code 1, error type and message), file I/O errors (exit code 1, file path and error), invalid artifact type (exit code 1, usage help)
- Versioning expectations: not applicable for v1

### Workflow Engine Interface

- Name: `WorkflowEngine`
- Inputs:
  - `checkPrerequisites(artifactType: ArtifactType): PrerequisiteResult`
  - `getStatus(): WorkflowStatus`
  - `getNextStep(): ArtifactType | null`
- Outputs:
  ```
  type ArtifactType = 'prd' | 'acf' | 'sad' | 'dcf' | 'tdd' | 'wdd' | 'ord'
  type ArtifactState = 'not_exists' | 'draft' | 'validated' | 'frozen'
  interface PrerequisiteResult { ok: boolean; missing: ArtifactType[] }
  interface WorkflowStatus { artifacts: Record<ArtifactType, ArtifactState> }
  ```
- Error modes: throws if artifact type is unknown
- Versioning expectations: not applicable

### Prompt Assembler Interface

- Name: `PromptAssembler`
- Inputs:
  - `assembleGeneratePrompt(artifactType: ArtifactType): string` — returns the full prompt string for generation
  - `assembleValidatePrompt(artifactType: ArtifactType): string` — returns the full prompt string for validation
- Outputs: a single string containing the concatenated prompt, template (for generation), upstream artifacts (for generation), validator (for validation), and target artifact (for validation)
- Error modes: throws if kit files not found at configured path, throws if required upstream artifacts not found
- Versioning expectations: not applicable

### LLM Client Interface

- Name: `LLMProvider` (interface) + `AnthropicProvider` (implementation)
- Inputs:
  ```
  interface LLMProvider {
    generate(prompt: string): Promise<string>
  }
  interface LLMConfig {
    apiKey: string
    model: string
    maxTokens: number
  }
  ```
- Outputs: `Promise<string>` — the LLM response text
- Error modes: throws `LLMAuthError` on 401, `LLMRateLimitError` on 429, `LLMTimeoutError` on timeout, `LLMError` for other API errors. Each error type includes the original error message.
- Versioning expectations: the `LLMProvider` interface is the extension point for future providers

### File Manager Interface

- Name: `FileManager`
- Inputs:
  - `readKitFile(relativePath: string): string` — reads a file from the kit directory
  - `readArtifact(artifactType: ArtifactType): string | null` — reads an artifact from `docs/sdlc/`, returns null if not found
  - `writeArtifact(artifactType: ArtifactType, content: string): void` — writes artifact to `docs/sdlc/`
  - `writeValidation(artifactType: ArtifactType, json: string): void` — writes validation JSON
  - `getArtifactState(artifactType: ArtifactType): ArtifactState` — checks existence and parses Status field
  - `freezeArtifact(artifactType: ArtifactType, approvedBy: string): void` — updates Status to Frozen, writes freeze declaration
- Outputs: file contents as strings, artifact state as `ArtifactState` enum
- Error modes: throws on file read/write failures with path and OS error details
- Versioning expectations: not applicable

### Artifact File Naming Convention

| Artifact Type | File Number | Filename |
|--------------|-------------|----------|
| prd | 01 | `01-prd.md` |
| acf | 02 | `02-acf.md` |
| sad | 03 | `03-sad.md` |
| dcf | 04 | `04-dcf.md` |
| tdd | 05 | `05-tdd.md` |
| wdd | 06 | `06-wdd.md` |
| ord | 07 | `07-ord.md` |

Validation files follow: `{nn}-{type}-validation.json`

### Artifact Flow Prerequisites

| Artifact | Prerequisites (must be frozen) |
|----------|-------------------------------|
| prd | none (product brief is input, not a managed artifact) |
| acf | none (architecture context is input, not a managed artifact) |
| sad | prd, acf |
| dcf | none (design context is input, not a managed artifact) |
| tdd | sad, dcf |
| wdd | tdd |
| ord | wdd |

## 5. Build and Deployment Approach (Deterministic)

- Build steps (high level but explicit):
  1. Install dependencies: `npm install`
  2. Compile TypeScript: `npx tsc` (tsconfig.json targets ESM, strict mode, Node.js 20)
  3. Output directory: `dist/`
  4. Entry point: `dist/cli.js` with `#!/usr/bin/env node` shebang

- Deployment steps (high level but explicit):
  1. For npm distribution: `npm publish` publishes the package. Users install with `npm install -g ai-sdlc`.
  2. For local development: clone the repo, `npm install`, `npm run build`, then `node dist/cli.js` or `npm link` for global access.
  3. The `bin` field in `package.json` maps `sdlc` to `dist/cli.js`.

- Configuration inputs required:
  - Kit path: location of ai-sdlc-kit files. Resolved in order: `--kit-path` CLI flag → `kitPath` in `.sdlcrc.json` → default to `node_modules/ai-sdlc/docs/` (for npm installs) → current directory `docs/` (fallback).
  - LLM model: defaults to `claude-sonnet-4-20250514`. Overridable via `--model` CLI flag or `model` in `.sdlcrc.json`.
  - Max tokens: defaults to `8192`. Overridable via `.sdlcrc.json`.

- Secrets required (names only; no values):
  - `ANTHROPIC_API_KEY` — read from environment variable only

### Configuration File Format (resolves SAD Deferred Decision 2)

`.sdlcrc.json` in the project root:
```
{
  "kitPath": "./path/to/kit",
  "model": "claude-sonnet-4-20250514",
  "maxTokens": 8192
}
```
All fields optional. CLI flags override config file values. Config file is safe to commit (no secrets).

## 6. Failure Handling and Rollback (Hard)

- Failure modes:
  - LLM API timeout: SDK throws timeout error
  - LLM API rate limit: SDK throws 429 error
  - LLM API auth failure: SDK throws 401 error
  - Kit files not found: `readKitFile` throws file-not-found error
  - Upstream artifact not found: `readArtifact` returns null when expected
  - Malformed validation JSON: `JSON.parse` throws on LLM response
  - Disk write failure: `writeArtifact` throws OS-level error

- Detection signals:
  - All errors are caught at the command handler level via try/catch
  - Error type is identified by error class (`LLMAuthError`, `LLMRateLimitError`, `LLMTimeoutError`, `LLMError`, Node.js file system errors)

- Rollback or compensation behavior:
  - Generation: artifact file is written only after successful LLM response. If the write fails, no partial file is left (write to a temp file, then rename — atomic write).
  - Validation: validation JSON is written only after successful parse. If parse fails, no validation file is written.
  - Freeze: Status field is updated in-place. If the write fails during freeze, the artifact remains in its previous state (Draft). The user re-runs `sdlc freeze`.

- Partial failure behavior:
  - Each command is a single operation. There are no multi-step transactions that can partially complete.
  - If the LLM call succeeds but the file write fails, the LLM response is lost and the user must regenerate.

## 7. Observability (Hard)

- Logs:
  - All output goes to stdout (success messages, status tables, validation results) and stderr (error messages).
  - No log files. No log levels in v1. Errors include the failure source and actionable guidance.

- Metrics:
  - Not applicable — local CLI tool, no metrics infrastructure.

- Traces (if applicable):
  - Not applicable — no distributed tracing.

- Evidence required to prove success:
  - `sdlc generate <type>`: artifact file exists in `docs/sdlc/` with `Status: Draft`
  - `sdlc validate <type>`: validation JSON file exists with `status: "PASS"` or `status: "FAIL"`
  - `sdlc freeze <type>`: artifact file has `Status: Frozen` and freeze declaration fields populated
  - `sdlc status`: output table reflects current file state on disk

### Error Message Format (resolves SAD Deferred Decision 6)

All error messages follow this pattern:
```
Error: [SOURCE] description
  → actionable guidance
```

Examples:
- `Error: [LLM API] Authentication failed (401)` → `Check that ANTHROPIC_API_KEY is set correctly`
- `Error: [Workflow] Cannot generate SAD — prerequisites not frozen` → `Missing: prd (not_exists), acf (draft)`
- `Error: [File] Cannot read kit file: prompts/prd-prompt.md` → `Check kit path configuration (current: ./docs/)`

## 8. Testing Strategy (Hard)

- Unit tests:
  - Workflow Engine: test prerequisite checking for each artifact type, test state detection, test next-step logic
  - Prompt Assembler: test file concatenation logic with mock file contents
  - File Manager: test artifact state parsing from Markdown Status field, test file naming conventions, test atomic write behavior
  - LLM Client: test error classification (auth, rate limit, timeout, generic)

- Integration tests:
  - Generate command: mock LLM response, verify artifact file is written with correct content and Status: Draft
  - Validate command: mock LLM response with PASS/FAIL JSON, verify validation file is written and output is correct
  - Freeze command: set up artifact file with Status: Draft, verify it is updated to Status: Frozen with freeze declaration
  - Status command: set up various artifact files, verify output table is accurate
  - Prerequisite enforcement: attempt to generate an artifact with missing prerequisites, verify error message

- System/acceptance tests:
  - Not required for v1 per DCF (no end-to-end tests against live LLM API due to cost and non-determinism).

- Failure tests:
  - LLM API auth error: mock 401 response, verify error message includes `[LLM API]` source and guidance to check API key
  - LLM API timeout: mock timeout, verify error message and that no artifact file is written
  - Missing kit files: configure invalid kit path, verify error message includes `[File]` source and path
  - Malformed validation response: mock non-JSON LLM response during validation, verify error is caught and no validation file is written
  - Missing prerequisites: attempt `sdlc generate sad` with no frozen PRD/ACF, verify error lists missing prerequisites

- Pass/fail criteria:
  - All unit and integration tests pass
  - Zero TypeScript compilation errors in strict mode
  - Test coverage for core modules (Workflow Engine, File Manager, Prompt Assembler) exceeds 80%

## 9. Operational Notes (Minimum Runbook)

- Deploy procedure:
  1. `npm publish` to publish a new version
  2. Users update with `npm install -g ai-sdlc@latest`
  3. No server deployment — the CLI runs locally

- Verify procedure:
  1. After installation, run `sdlc status` in any directory
  2. Expected: CLI outputs the artifact status table (all "not_exists" if no artifacts present)
  3. If `ANTHROPIC_API_KEY` is not set and the user runs `sdlc generate`, the error message should indicate the missing API key

- Rollback procedure:
  1. `npm install -g ai-sdlc@<previous-version>` to revert to a prior version
  2. No data migration needed — artifact files on disk are independent of CLI version

- Ownership/On-call expectations:
  - Project maintainer is responsible for releases and bug fixes
  - No on-call — this is a local CLI tool with no production service

## 10. Dependencies

- Dependency 1: `@anthropic-ai/sdk` — Anthropic Claude API SDK (runtime)
- Dependency 2: `commander` — CLI argument parsing (runtime)
- Dependency 3: `typescript` — TypeScript compiler (dev)
- Dependency 4: `vitest` — test framework (dev)

No other runtime dependencies.

## 11. Risks and Assumptions

- Risks:
  - LLM output quality varies — generated artifacts may not pass validation on first attempt. Mitigation: the validation loop is a core workflow feature.
  - Large upstream artifacts may exceed LLM context windows. Mitigation: kit artifacts are designed to be concise. The CLI logs a warning if the assembled prompt exceeds a configurable token threshold (default: 100,000 characters as a rough proxy).
  - LLM response format for validation may not be reliably parseable as JSON. Mitigation: the validator prompt explicitly requests JSON output. The CLI attempts `JSON.parse` and reports a clear error if it fails, allowing the user to re-run validation.
  - Atomic file writes depend on `rename()` working correctly on the target filesystem. Mitigation: this is reliable on all standard macOS and Linux filesystems.

- Assumptions:
  - The user has a working Anthropic API key configured as `ANTHROPIC_API_KEY` in their environment.
  - The ai-sdlc-kit files are accessible on disk at the configured or default kit path.
  - The project follows the `docs/sdlc/` convention for artifact storage.
  - The Anthropic SDK handles HTTPS, connection management, and request serialization.
  - Artifact Markdown files contain a `Status:` field in the Document Control section that can be parsed to determine freeze state.

### Resolution of SAD Deferred Decisions

| SAD Deferred Decision | TDD Resolution |
|----------------------|----------------|
| Deferred 1: Freeze state tracking mechanism | Freeze state is tracked via the `Status:` field in the artifact Markdown's Document Control section. The File Manager parses this field to determine state (Draft, Frozen). |
| Deferred 2: Configuration file format and location | `.sdlcrc.json` in the project root. All fields optional. CLI flags override config values. See Section 5. |
| Deferred 3: LLM provider interface contract | `LLMProvider` interface with a single `generate(prompt: string): Promise<string>` method. `AnthropicProvider` implements it. See Section 4. |
| Deferred 4: Execution loop implementation details | Execution loop phases are invoked as subcommands under `sdlc exec <phase> <work-item-id>` where phase is `test`, `plan`, `code`, or `review`. Each phase reads the frozen WDD, extracts the relevant work item, and assembles the appropriate prompt. State tracking follows the same file-based pattern. |
| Deferred 5: Streaming vs. buffered LLM responses | Buffered responses in v1. The `generate()` method returns the complete response string. Streaming can be added later by extending the interface without breaking existing callers. |
| Deferred 6: Error message formatting and verbosity levels | All errors follow the `Error: [SOURCE] description → guidance` pattern. No verbosity levels in v1. See Section 7. |

## 12. Readiness Checklist (Self-Check)
- [x] Scope and non-goals explicit and aligned with SAD
- [x] Interfaces and contracts explicit
- [x] Build/deploy steps deterministic
- [x] Failure and rollback behavior defined
- [x] Tests defined with pass/fail criteria
- [x] No unresolved decisions

## 13. Freeze Declaration (when ready)
This TDD is approved and frozen. Downstream artifacts may not reinterpret or expand this design.

- Approved By: Project Maintainer
- Date: 2026-02-17
