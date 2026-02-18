# WDD Template (Work Design Document)

Translate a frozen TDD into **atomic, executable work items** suitable for AI agents or humans.

## 0. Document Control
- WDD ID: WDD-CLI-001
- Author: AI-generated from TDD-CLI-001
- Date: 2026-02-17
- Status: Frozen
- Parent TDD:
  - TDD ID / Link: TDD-CLI-001
  - TDD Status: Frozen (required)

## 1. Scope and Non-Goals (Copied from TDD)

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

## 2. Work Items

### WDD Item
- WDD Item ID: WDD-CLI-001-01
- Parent TDD Section: Section 5 (Build and Deployment), Section 10 (Dependencies)
- Assignee Type: Either

#### Intent (1–2 sentences)
Initialize the project scaffolding: `package.json`, `tsconfig.json`, directory structure, and dev/runtime dependencies so that subsequent work items have a buildable project to work in.

#### In Scope
- Create `package.json` with `name`, `version`, `type: "module"`, `bin` field mapping `sdlc` to `dist/cli.js`, runtime dependencies (`@anthropic-ai/sdk`, `commander`), dev dependencies (`typescript`, `vitest`)
- Create `tsconfig.json` targeting ESM, strict mode, Node.js 20, output to `dist/`
- Create directory structure: `src/`, `src/commands/`, `src/llm/`
- Create a minimal `src/cli.ts` entry point that prints a version placeholder (to verify the build pipeline works)
- Add `build` and `test` scripts to `package.json`

#### Out of Scope / Non-Goals
- No actual command implementation (just the skeleton)
- No publishing to npm

#### Inputs
- TDD Section 5 (build steps, directory structure, entry point)
- TDD Section 10 (dependency list)

#### Outputs
- `package.json` with correct fields
- `tsconfig.json` with correct compiler options
- Directory structure: `src/`, `src/commands/`, `src/llm/`
- `src/cli.ts` with shebang and version placeholder
- Successful `npm install` and `npm run build`

#### Acceptance Criteria (Executable)
- AC1: Given the project is initialized, when `npm install` is run, then it completes without errors.
  - Failure: If `npm install` fails, dependency specification is incorrect.
- AC2: Given dependencies are installed, when `npm run build` is run, then TypeScript compiles to `dist/` without errors.
  - Failure: If compilation fails, `tsconfig.json` or source files are misconfigured.
- AC3: Given the project is built, when `node dist/cli.js --version` is run, then it prints a version string.
  - Failure: If no output or error, the entry point or shebang is incorrect.

#### Definition of Done (Hard)
- [ ] PR merged
- [ ] `npm install` succeeds
- [ ] `npm run build` succeeds with zero TypeScript errors
- [ ] `node dist/cli.js` runs without error

#### Dependencies
None — this is the first work item.

#### Rollback / Failure Behavior
Delete the created files and start over. No existing state to corrupt.

---

### WDD Item
- WDD Item ID: WDD-CLI-001-02
- Parent TDD Section: Section 4 (File Manager Interface)
- Assignee Type: Either

#### Intent (1–2 sentences)
Implement the File Manager module that reads/writes artifact files and kit files, parses artifact state from the `Status:` field, and supports atomic writes.

#### In Scope
- Implement `src/files.ts` with the `FileManager` interface from TDD Section 4
- `readKitFile(relativePath)`: reads a file from the configured kit directory
- `readArtifact(artifactType)`: reads an artifact from `docs/sdlc/`, returns null if not found
- `writeArtifact(artifactType, content)`: atomic write (temp file + rename) to `docs/sdlc/{nn}-{type}.md`
- `writeValidation(artifactType, json)`: writes validation JSON to `docs/sdlc/{nn}-{type}-validation.json`
- `getArtifactState(artifactType)`: parses `Status:` field from artifact Markdown, returns `ArtifactState`
- `freezeArtifact(artifactType, approvedBy)`: updates `Status: Draft` to `Status: Frozen`, writes freeze declaration
- Artifact file naming convention per TDD Section 4 table
- Shared types: `ArtifactType`, `ArtifactState`

#### Out of Scope / Non-Goals
- No CLI integration (tested in isolation)
- No LLM calls

#### Inputs
- TDD Section 4 (File Manager Interface, Artifact File Naming Convention)
- TDD Section 6 (atomic write behavior)

#### Outputs
- `src/files.ts` implementing `FileManager`
- `src/types.ts` with shared type definitions (`ArtifactType`, `ArtifactState`)
- Unit tests in `src/__tests__/files.test.ts`

#### Acceptance Criteria (Executable)
- AC1: Given a kit directory with a file `prompts/prd-prompt.md`, when `readKitFile('prompts/prd-prompt.md')` is called, then the file contents are returned as a string.
  - Failure: If the file does not exist, throws an error with `[File]` source and the file path.
- AC2: Given an artifact file `docs/sdlc/01-prd.md` with `Status: Frozen`, when `getArtifactState('prd')` is called, then it returns `'frozen'`.
  - Failure: If parsing fails or returns wrong state, the regex or parsing logic is incorrect.
- AC3: Given `writeArtifact('prd', content)` is called, when the write succeeds, then `docs/sdlc/01-prd.md` contains the content and no temp file remains.
  - Failure: If the process is interrupted during write, no partial file exists (atomic write guarantee).
- AC4: Given an artifact with `Status: Draft`, when `freezeArtifact('prd', 'Project Maintainer')` is called, then the file is updated to `Status: Frozen` with the approver and date in the freeze declaration.
  - Failure: If the status field is not found, throws an error.

#### Definition of Done (Hard)
- [ ] PR merged
- [ ] Unit tests passing for all FileManager methods
- [ ] Atomic write behavior verified in tests
- [ ] Tests passing (Vitest)

#### Dependencies
- WDD-CLI-001-01 (project scaffolding must exist)

#### Rollback / Failure Behavior
Revert the PR. No external state affected.

---

### WDD Item
- WDD Item ID: WDD-CLI-001-03
- Parent TDD Section: Section 4 (Workflow Engine Interface)
- Assignee Type: Either

#### Intent (1–2 sentences)
Implement the Workflow Engine module that enforces artifact flow order, checks prerequisite freeze state, and determines the next step in the workflow.

#### In Scope
- Implement `src/workflow.ts` with the `WorkflowEngine` interface from TDD Section 4
- `checkPrerequisites(artifactType)`: checks that all prerequisite artifacts are frozen per the prerequisite table
- `getStatus()`: returns the state of all artifacts by querying File Manager
- `getNextStep()`: returns the next artifact type that needs attention, or null if all are frozen
- Artifact flow prerequisite definitions per TDD Section 4 table

#### Out of Scope / Non-Goals
- No CLI integration (tested in isolation)
- No LLM calls
- No file I/O (delegates to File Manager)

#### Inputs
- TDD Section 4 (Workflow Engine Interface, Artifact Flow Prerequisites table)
- File Manager module (WDD-CLI-001-02)

#### Outputs
- `src/workflow.ts` implementing `WorkflowEngine`
- Unit tests in `src/__tests__/workflow.test.ts`

#### Acceptance Criteria (Executable)
- AC1: Given PRD and ACF are frozen, when `checkPrerequisites('sad')` is called, then it returns `{ ok: true, missing: [] }`.
  - Failure: If PRD or ACF are not frozen, returns `{ ok: false, missing: ['prd'] }` (or whichever is missing).
- AC2: Given no artifacts exist, when `checkPrerequisites('prd')` is called, then it returns `{ ok: true, missing: [] }` (PRD has no prerequisites).
  - Failure: If it incorrectly requires prerequisites for PRD, the prerequisite table is wrong.
- AC3: Given some artifacts are frozen and others are not, when `getNextStep()` is called, then it returns the first artifact type whose prerequisites are met but which is not yet frozen.
  - Failure: If it returns an artifact whose prerequisites are not met, the logic is incorrect.

#### Definition of Done (Hard)
- [ ] PR merged
- [ ] Unit tests passing for all WorkflowEngine methods
- [ ] All prerequisite combinations tested
- [ ] Tests passing (Vitest)

#### Dependencies
- WDD-CLI-001-02 (File Manager, for `getArtifactState` queries)

#### Rollback / Failure Behavior
Revert the PR. No external state affected.

---

### WDD Item
- WDD Item ID: WDD-CLI-001-04
- Parent TDD Section: Section 4 (Prompt Assembler Interface)
- Assignee Type: Either

#### Intent (1–2 sentences)
Implement the Prompt Assembler module that reads kit files and frozen upstream artifacts and concatenates them into a single prompt string for the LLM.

#### In Scope
- Implement `src/assembler.ts` with the `PromptAssembler` interface from TDD Section 4
- `assembleGeneratePrompt(artifactType)`: concatenates prompt file + template file + frozen upstream artifact contents
- `assembleValidatePrompt(artifactType)`: concatenates validator file + target artifact content
- Mapping from artifact type to kit file paths (e.g., `prd` → `prompts/prd-prompt.md`, `artifacts/prd-template.md`, `validators/prd-validator.md`)
- Mapping from artifact type to required upstream artifacts

#### Out of Scope / Non-Goals
- No LLM calls (just string assembly)
- No CLI integration

#### Inputs
- TDD Section 4 (Prompt Assembler Interface)
- File Manager module (WDD-CLI-001-02)
- Kit file naming conventions from the ai-sdlc-kit repository

#### Outputs
- `src/assembler.ts` implementing `PromptAssembler`
- Unit tests in `src/__tests__/assembler.test.ts`

#### Acceptance Criteria (Executable)
- AC1: Given kit files exist and PRD has no upstream artifacts, when `assembleGeneratePrompt('prd')` is called, then it returns a string containing the prompt file contents followed by the template file contents.
  - Failure: If kit files are not found, throws an error with `[File]` source.
- AC2: Given a frozen PRD artifact exists, when `assembleValidatePrompt('prd')` is called, then it returns a string containing the validator file contents followed by the PRD artifact contents.
  - Failure: If the artifact is not found, throws an error.
- AC3: Given kit files exist and PRD + ACF are frozen, when `assembleGeneratePrompt('sad')` is called, then the returned string contains the SAD prompt, SAD template, frozen PRD content, and frozen ACF content.
  - Failure: If any upstream artifact is missing, throws an error listing the missing artifact.

#### Definition of Done (Hard)
- [ ] PR merged
- [ ] Unit tests passing for all PromptAssembler methods
- [ ] Tests cover all artifact types
- [ ] Tests passing (Vitest)

#### Dependencies
- WDD-CLI-001-02 (File Manager)

#### Rollback / Failure Behavior
Revert the PR. No external state affected.

---

### WDD Item
- WDD Item ID: WDD-CLI-001-05
- Parent TDD Section: Section 4 (LLM Client Interface)
- Assignee Type: Either

#### Intent (1–2 sentences)
Implement the LLM Client module with the provider interface and the Anthropic Claude implementation, including typed error classes for different API failure modes.

#### In Scope
- Implement `src/llm/client.ts` with the `LLMProvider` interface and `LLMConfig` type
- Implement `src/llm/anthropic.ts` with `AnthropicProvider` implementing `LLMProvider`
- `generate(prompt)`: sends prompt to Anthropic API via `@anthropic-ai/sdk`, returns response text
- Error classes: `LLMError`, `LLMAuthError`, `LLMRateLimitError`, `LLMTimeoutError`
- API key read from `LLMConfig.apiKey` (caller provides it from environment variable)
- Buffered responses (complete response string, no streaming)

#### Out of Scope / Non-Goals
- No streaming in v1
- No other LLM providers
- No CLI integration

#### Inputs
- TDD Section 4 (LLM Client Interface)
- `@anthropic-ai/sdk` package

#### Outputs
- `src/llm/client.ts` with `LLMProvider` interface, `LLMConfig` type, error classes
- `src/llm/anthropic.ts` with `AnthropicProvider` implementation
- Unit tests in `src/__tests__/llm.test.ts`

#### Acceptance Criteria (Executable)
- AC1: Given a valid API key and prompt, when `generate(prompt)` is called with a mocked SDK, then it returns the response text string.
  - Failure: If the SDK call fails unexpectedly, wraps the error in `LLMError`.
- AC2: Given the SDK throws a 401 error, when `generate(prompt)` is called, then it throws `LLMAuthError` with the original message.
  - Failure: If the error is not classified correctly, the error handler logic is wrong.
- AC3: Given the SDK throws a 429 error, when `generate(prompt)` is called, then it throws `LLMRateLimitError`.
  - Failure: If the error is misclassified.
- AC4: Given the SDK throws a timeout error, when `generate(prompt)` is called, then it throws `LLMTimeoutError`.
  - Failure: If the error is misclassified.

#### Definition of Done (Hard)
- [ ] PR merged
- [ ] Unit tests passing with mocked SDK for success and all error types
- [ ] Tests passing (Vitest)

#### Dependencies
- WDD-CLI-001-01 (project scaffolding, `@anthropic-ai/sdk` dependency)

#### Rollback / Failure Behavior
Revert the PR. No external state affected.

---

### WDD Item
- WDD Item ID: WDD-CLI-001-06
- Parent TDD Section: Section 4 (CLI Interface), Section 3 (Key Flows)
- Assignee Type: Either

#### Intent (1–2 sentences)
Implement the CLI entry point and all four command handlers (`generate`, `validate`, `freeze`, `status`) that wire together the Workflow Engine, Prompt Assembler, LLM Client, and File Manager.

#### In Scope
- Implement `src/cli.ts` with commander-based argument parsing and routing to command handlers
- Implement `src/commands/generate.ts`: check prerequisites → assemble prompt → call LLM → write artifact → print confirmation
- Implement `src/commands/validate.ts`: assemble validation prompt → call LLM → parse JSON → write validation file → print PASS/FAIL summary
- Implement `src/commands/freeze.ts`: check artifact exists and has passing validation → prompt user for confirmation → freeze artifact → print confirmation
- Implement `src/commands/status.ts`: get workflow status → print artifact status table
- Configuration resolution: `--kit-path` flag, `--model` flag, `.sdlcrc.json` loading, environment variable for API key
- Error handling at command level: catch all errors, format with `Error: [SOURCE] description → guidance` pattern
- Exit codes: 0 for success, 1 for errors

#### Out of Scope / Non-Goals
- No execution loop commands (`sdlc exec`) — separate work item
- No npm publishing

#### Inputs
- TDD Section 3 (Key Flows), Section 4 (CLI Interface), Section 5 (Configuration), Section 7 (Error Message Format)
- All component modules (WDD-CLI-001-02 through WDD-CLI-001-05)

#### Outputs
- `src/cli.ts` (entry point with commander setup)
- `src/commands/generate.ts`
- `src/commands/validate.ts`
- `src/commands/freeze.ts`
- `src/commands/status.ts`
- `src/config.ts` (configuration resolution logic)
- Integration tests in `src/__tests__/commands.test.ts`

#### Acceptance Criteria (Executable)
- AC1: Given prerequisites are frozen and LLM returns content, when `sdlc generate prd` is run, then `docs/sdlc/01-prd.md` is created with `Status: Draft` and a confirmation message is printed.
  - Failure: If prerequisites are not frozen, prints error with `[Workflow]` source listing missing artifacts and exits with code 1.
- AC2: Given an artifact exists and LLM returns valid JSON, when `sdlc validate prd` is run, then the validation JSON is written and PASS/FAIL summary is printed.
  - Failure: If LLM returns non-JSON, prints error with `[LLM API]` source and no validation file is written.
- AC3: Given an artifact has a passing validation, when `sdlc freeze prd` is run and user confirms, then the artifact's `Status:` is updated to `Frozen` and freeze declaration is populated.
  - Failure: If no passing validation exists, prints error and exits with code 1.
- AC4: Given various artifacts in different states, when `sdlc status` is run, then it prints a table showing each artifact type, its state, and the next step.
  - Failure: If the table does not reflect actual file state, the status logic is incorrect.
- AC5: Given `ANTHROPIC_API_KEY` is not set, when any LLM command is run, then it prints `Error: [LLM API] Authentication failed` with guidance to check the API key.
  - Failure: If the error is not caught or message is unclear.

#### Definition of Done (Hard)
- [ ] PR merged
- [ ] Integration tests passing with mocked LLM for all four commands
- [ ] Error formatting matches TDD pattern
- [ ] Tests passing (Vitest)

#### Dependencies
- WDD-CLI-001-02 (File Manager)
- WDD-CLI-001-03 (Workflow Engine)
- WDD-CLI-001-04 (Prompt Assembler)
- WDD-CLI-001-05 (LLM Client)

#### Rollback / Failure Behavior
Revert the PR. No external state affected.

---

### WDD Item
- WDD Item ID: WDD-CLI-001-07
- Parent TDD Section: Section 8 (Testing Strategy)
- Assignee Type: Either

#### Intent (1–2 sentences)
Implement the failure test suite that verifies error handling across all failure modes defined in the TDD, ensuring no data corruption occurs on failure.

#### In Scope
- LLM API auth error test: mock 401, verify `[LLM API]` error message with API key guidance
- LLM API timeout test: mock timeout, verify error message and no artifact file written
- LLM API rate limit test: mock 429, verify error message
- Missing kit files test: configure invalid kit path, verify `[File]` error with path
- Malformed validation response test: mock non-JSON LLM response, verify error caught and no validation file written
- Missing prerequisites test: attempt `sdlc generate sad` with no frozen PRD/ACF, verify `[Workflow]` error listing missing artifacts
- Disk write failure test: mock file write error, verify error message and no partial file

#### Out of Scope / Non-Goals
- No new feature code — tests only
- No end-to-end tests against live LLM API

#### Inputs
- TDD Section 6 (Failure Handling), Section 7 (Error Message Format), Section 8 (Failure Tests)

#### Outputs
- `src/__tests__/failures.test.ts`

#### Acceptance Criteria (Executable)
- AC1: Given all failure test cases from TDD Section 8 are implemented, when `npm test` is run, then all failure tests pass.
  - Failure: If any failure test does not pass, the error handling for that failure mode is incorrect.
- AC2: Given each failure test verifies both the error message format and the absence of side effects (no files written, no files corrupted), when tests run, then all assertions pass.
  - Failure: If a failure mode produces a side effect (partial file, wrong state), the rollback behavior is broken.

#### Definition of Done (Hard)
- [ ] PR merged
- [ ] All TDD-specified failure tests implemented and passing
- [ ] Tests passing (Vitest)

#### Dependencies
- WDD-CLI-001-06 (CLI commands must be implemented to test failure handling)

#### Rollback / Failure Behavior
Revert the PR. No external state affected.

---

### WDD Item
- WDD Item ID: WDD-CLI-001-08
- Parent TDD Section: Section 11 (Resolution of SAD Deferred Decision 4)
- Assignee Type: Either

#### Intent (1–2 sentences)
Implement the execution loop command (`sdlc exec <phase> <work-item-id>`) that supports the test, plan, code, and review phases for individual WDD work items.

#### In Scope
- Add `exec` command to the CLI: `sdlc exec <phase> <work-item-id>` where phase is `test`, `plan`, `code`, or `review`
- Read the frozen WDD, extract the specified work item by ID
- Assemble the appropriate prompt for the phase (using kit execution loop prompts if available, or a built-in prompt structure)
- Call LLM and display the result
- File-based state tracking for execution phases (same pattern as artifact state)

#### Out of Scope / Non-Goals
- No modification to the core generate/validate/freeze/status commands
- No new dependencies

#### Inputs
- TDD Section 11 (Deferred Decision 4 resolution)
- Frozen WDD artifact on disk
- Kit execution loop prompts (if available)

#### Outputs
- `src/commands/exec.ts`
- Updated `src/cli.ts` to register the `exec` command
- Unit/integration tests in `src/__tests__/exec.test.ts`

#### Acceptance Criteria (Executable)
- AC1: Given a frozen WDD with work item `WDD-CLI-001-01`, when `sdlc exec plan WDD-CLI-001-01` is run with a mocked LLM, then the LLM is called with a prompt containing the work item details and the response is displayed.
  - Failure: If the WDD is not frozen, prints error with `[Workflow]` source and exits with code 1.
- AC2: Given a valid phase and work item ID, when the command runs, then the output is displayed to stdout.
  - Failure: If the work item ID is not found in the WDD, prints error with `[Workflow]` source.
- AC3: Given an invalid phase name, when the command runs, then it prints usage help and exits with code 1.
  - Failure: If it does not reject invalid phases.

#### Definition of Done (Hard)
- [ ] PR merged
- [ ] Tests passing for all four phases with mocked LLM
- [ ] Tests passing (Vitest)

#### Dependencies
- WDD-CLI-001-06 (CLI entry point and command pattern)

#### Rollback / Failure Behavior
Revert the PR. No external state affected.

---

### WDD Item
- WDD Item ID: WDD-CLI-001-09
- Parent TDD Section: Section 5 (Build and Deployment), Section 9 (Operational Notes)
- Assignee Type: Human

#### Intent (1–2 sentences)
Prepare the package for npm distribution: finalize `package.json` metadata, add README with installation and usage instructions, verify the build-publish pipeline works.

#### In Scope
- Finalize `package.json`: description, keywords, license, repository, files array (include `dist/` only)
- Add a README.md with: installation (`npm install -g ai-sdlc`), basic usage examples for each command, configuration (`.sdlcrc.json`, environment variables)
- Verify `npm pack` produces a clean tarball with only intended files
- Verify `npm install -g` from the tarball works and `sdlc` is available on PATH

#### Out of Scope / Non-Goals
- No actual `npm publish` (that requires human authorization)
- No CI/CD pipeline

#### Inputs
- TDD Section 5 (Deployment steps), Section 9 (Operational Notes)

#### Outputs
- Updated `package.json` with distribution metadata
- `README.md` with installation and usage documentation
- Verified `npm pack` output

#### Acceptance Criteria (Executable)
- AC1: Given the package is built, when `npm pack` is run, then the tarball contains `dist/`, `package.json`, and `README.md` only (no `src/`, no test files).
  - Failure: If extra files are included, the `files` field in `package.json` is misconfigured.
- AC2: Given the tarball, when `npm install -g <tarball>` is run, then `sdlc status` is available and runs without error.
  - Failure: If the command is not found, the `bin` field is misconfigured.

#### Definition of Done (Hard)
- [ ] PR merged
- [ ] `npm pack` produces clean tarball
- [ ] Local install from tarball verified
- [ ] README.md exists with installation and usage docs

#### Dependencies
- WDD-CLI-001-06 (all CLI commands implemented)
- WDD-CLI-001-08 (exec command implemented)

#### Rollback / Failure Behavior
Revert the PR. No external state affected.

---

## 3. Work Groups

### Work Group
- Group ID: WG-01
- Group Name: Foundation
- Business Capability: A buildable, testable project structure exists with shared types and the File Manager component that all other work depends on.
- Member Items: WDD-CLI-001-01, WDD-CLI-001-02
- Acceptance Criteria (Group-Level): The project compiles, tests run, and the File Manager can read/write artifact files with correct state detection.

### Work Group
- Group ID: WG-02
- Group Name: Core Components
- Business Capability: All internal components (Workflow Engine, Prompt Assembler, LLM Client) work in isolation and are ready to be wired into the CLI.
- Member Items: WDD-CLI-001-03, WDD-CLI-001-04, WDD-CLI-001-05
- Acceptance Criteria (Group-Level): Each component passes its unit tests independently. Prerequisite checking, prompt assembly, and LLM communication are verified with mocks.

### Work Group
- Group ID: WG-03
- Group Name: CLI Integration
- Business Capability: A user can run the full artifact workflow (generate, validate, freeze, status) from the command line, end to end with mocked LLM responses.
- Member Items: WDD-CLI-001-06, WDD-CLI-001-07
- Acceptance Criteria (Group-Level): All four CLI commands work correctly. All TDD-specified failure modes are tested and handled. Error messages follow the defined format.

### Work Group
- Group ID: WG-04
- Group Name: Execution Loop and Distribution
- Business Capability: The CLI supports execution loop phases and is ready for npm distribution.
- Member Items: WDD-CLI-001-08, WDD-CLI-001-09
- Acceptance Criteria (Group-Level): The `sdlc exec` command works for all four phases. The package installs cleanly from npm and all commands are accessible.

---

## 4. Mandatory Granularity Rules (Hard)

Every WDD item must satisfy all of the following:

- One observable outcome
- One pull request
- One subsystem or repo
- No design decisions
- No cross-environment deployment
- Completable in a short, bounded time window

Items violating these rules must be split. See the Splitting Guidance section in the playbook for patterns.

---

## 5. Freeze Declaration (when ready)
This WDD is approved and frozen. Execution may proceed.

- Approved By: Project Maintainer
- Date: 2026-02-17
