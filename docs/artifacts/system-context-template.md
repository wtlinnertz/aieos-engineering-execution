# System Context (SAD Intake for Existing Systems)

A lightweight intake form for capturing existing system architecture before generating a SAD.

This template is for **brownfield projects** where a system already exists. For greenfield projects, the SAD is generated directly from the frozen PRD and ACF — no intake form is needed.

Fill in what you know. Leave unknown sections blank — the SAD generation prompt will mark them as "Not provided."

**For existing codebases:** Run `codebase-analysis-prompt.md` to pre-fill this form from codebase analysis (Output C). Review the pre-filled form for accuracy before using it as SAD input.

---

## System Structure

- Directory structure and organizational pattern: (e.g., monorepo, layered, feature-based)
- Major components or modules: (list each with a brief description)
- Entry points: (main files, route definitions, handler registrations)
- Component boundaries: (how are modules separated — packages, directories, services?)

---

## External Integrations

- Databases and data stores: (type, connection patterns)
- External APIs consumed: (endpoints, client libraries)
- External APIs exposed: (route definitions, API frameworks)
- Message queues, event buses, or async communication:
- Third-party services: (auth providers, payment, email, etc.)

---

## Data Patterns

- Data models or schemas: (ORMs, schema definitions, type definitions)
- Data access patterns: (repository pattern, direct queries, data mappers)
- Data flow direction between components: (which components read vs. write)
- Data ownership: (which component is the authoritative source for each data store)

---

## Security Boundaries

- Authentication mechanism: (JWT, session, OAuth, etc.)
- Authorization approach: (RBAC, middleware guards, etc.)
- Trust boundaries: (public endpoints vs. internal, middleware chains)
- Input validation patterns:

---

## Existing Documentation

- README content summary:
- API documentation: (OpenAPI specs, generated docs)
- Architecture decision records: (if any)
- Other architecture or design documents:

---

## Known Architectural Concerns

- Components with unclear boundaries or responsibilities:
- Undocumented integrations or dependencies:
- Known technical debt or architectural gaps:
- Areas the team considers fragile or risky:

---

## Completeness Checklist

Before handing this to the SAD generation prompt, confirm:

- [ ] Major components are identified
- [ ] External integrations are listed
- [ ] Data stores and ownership are documented
- [ ] Security boundaries are identified
- [ ] Known concerns are captured for the SAD's Risks section
