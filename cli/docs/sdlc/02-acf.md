# ACF Template (Architecture Context File)

The ACF defines **architecture guardrails** that constrain the SAD and downstream artifacts.
It is reusable across many initiatives.

## 0. Document Control
- ACF ID: ACF-CLI-001
- Owner: Project Maintainer
- Date: 2026-02-17
- Status: Frozen
- Applies To: ai-sdlc CLI

## 1. Purpose
Enforce architectural guardrails for the ai-sdlc CLI: technology stack constraints, dependency limits, secrets handling, state management rules, and forbidden patterns that all downstream design and implementation must obey.

## 2. Platform Assumptions (High Level)
- Runtime environment(s): Node.js 20 LTS
- Deployment model(s): npm package (`npm install -g ai-sdlc`); cloning the repo and running directly also acceptable for v1
- Networking posture (high level): Outbound HTTPS only — the CLI calls LLM APIs over the network. No inbound connections. No listening ports.
- Identity model (high level): Not applicable — single-user local CLI tool. Authentication is to the LLM API via user-provided API key.

## 3. Security Guardrails (Hard)
- Authentication requirements: Not applicable at the application level. LLM API authentication uses API keys provided by the user.
- Authorization expectations: Not applicable — single-user local tool, no roles or permissions.
- Data classification and handling: Artifacts are project documentation (not sensitive by default). The CLI reads and writes Markdown files on the local filesystem.
- Encryption requirements (in transit / at rest): In transit: HTTPS for all LLM API calls (enforced by the Anthropic SDK). At rest: not applicable — files are on the user's local disk.
- Secrets handling expectations: API keys SHALL come from environment variables only (`ANTHROPIC_API_KEY`). No secrets in config files. Config files (e.g., `.sdlcrc.json`) are safe to commit to version control.

### Security Review Triggers
- Trigger 1: Changes to how API keys are read or passed to the LLM SDK
- Trigger 2: Addition of any new external network calls beyond the LLM API
- Trigger 3: Addition of any new runtime dependencies
- Trigger 4: Not applicable
- Trigger 5: Not applicable

When triggered, the review phase must include security-specific verification against the guardrails in this section.

## 4. Compliance / Regulatory Constraints (Hard)
- Constraint 1: No telemetry or data collection of any kind.
- Constraint 2: No secrets stored by the CLI — API keys come from environment variables or config files excluded from version control.
- Data retention / archival requirements: Not applicable — all artifacts are local Markdown files managed by the user.

## 5. Reliability & Resilience Guardrails (Hard)
- Availability expectations (qualitative): Local CLI tool — availability is determined by the user's machine and LLM API uptime. No SLA applies.
- Failure isolation expectations: LLM API errors (timeouts, rate limits, auth failures) SHALL be handled gracefully with clear error messages. Failures SHALL NOT corrupt existing artifact files on disk.
- Rollback expectations: Not applicable at the infrastructure level. Artifact files are under the user's version control.
- Disaster recovery expectations: Not applicable — stateless CLI, all state is files on disk under user control.

## 6. Observability Guardrails (Hard)
- Required telemetry types: None. No telemetry, no metrics collection, no tracing infrastructure.
- Minimum operational signals required: CLI SHALL output clear status messages and error messages to stdout/stderr. This is the only observability mechanism.

## 7. Approved Architectural Patterns
- Pattern 1: TypeScript (strict mode) with ESM (ES modules, not CommonJS).
- Pattern 2: Thin orchestration over direct LLM API calls — read files from disk, assemble prompt, call API, write output.
- Pattern 3: File-based state — artifact existence and freeze status tracked via files in `docs/sdlc/`.
- Pattern 4: Provider-swappable LLM interface — v1 implements Anthropic Claude only, but the design allows adding providers later.

## 8. Forbidden Patterns (Hard)
- Forbidden 1: No agent frameworks (LangGraph, AutoGen, CrewAI, or similar).
- Forbidden 2: No CommonJS modules — ESM only.
- Forbidden 3: No runtime dependencies beyond `@anthropic-ai/sdk` and `commander`. Any addition requires explicit approval.
- Forbidden 4: No persistent server, daemon, or background process.
- Forbidden 5: No database or external state store — state is files on disk only.
- Forbidden 6: No web UI, GUI, or browser-based interface.
- Forbidden 7: No modification to kit prompts, templates, or validators — consume as-is.
- Forbidden 8: No secrets in config files or committed files.

## 9. Standard Interfaces / Integrations (Optional)
- Change management expectations: Not provided.
- CI/CD expectations: Not applicable — the CLI is a local tool. CI/CD wraps it externally if desired.
- Artifact storage expectations: All artifacts stored as Markdown files in `docs/sdlc/` within the project directory.
- Audit expectations: Not provided.

## 10. Open Items
None.

## 11. Freeze Declaration (when ready)
This ACF is approved and frozen. SAD and downstream artifacts must comply.

- Approved By: Project Maintainer
- Date: 2026-02-17
