# Architecture Context Inputs — ai-sdlc CLI

Human-authored decisions that feed the ACF generation prompt.
This is an **input document**, not an AI-generated artifact.

---

## Technology Decisions

- **Runtime:** Node.js 20 LTS
- **Language:** TypeScript (strict mode)
- **Module system:** ESM (ES modules, not CommonJS)

## Dependencies

- **LLM SDK:** `@anthropic-ai/sdk` (Anthropic Claude only for v1, provider-swappable design)
- **CLI framework:** `commander`
- **Test framework:** Vitest (dev dependency)
- **Dependency philosophy:** Minimal runtime dependencies — Anthropic SDK and commander only. Vitest and TypeScript are dev dependencies. No other runtime dependencies unless strictly necessary.

## Configuration

- **Secrets:** Environment variables only (`ANTHROPIC_API_KEY`)
- **Project settings:** Config file (e.g., `.sdlcrc.json`) for kit path and project-level settings
- **No secrets in config files** — config files are safe to commit to version control

## State Management

- **State is files on disk** in `docs/sdlc/`
- No database, no external state store

## Distribution

- npm package (`npm install -g ai-sdlc`)
- Cloning the repo and running directly also acceptable for v1
