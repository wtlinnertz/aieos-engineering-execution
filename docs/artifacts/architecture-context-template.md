# Architecture Context (ACF Intake)

A lightweight intake form for capturing architectural decisions before generating an ACF.
This is a **human-authored input**, not an AI-generated artifact.

Fill in what you know. Leave unknown sections blank — the ACF generation prompt will mark them as "Not provided."

---

## Runtime and Language

- Runtime environment: (e.g., Node.js 20 LTS, Python 3.12, JVM 21)
- Language: (e.g., TypeScript strict mode, Python, Java, Go)
- Module system: (e.g., ESM, CommonJS, Python packages)

---

## Dependencies

- Key libraries or SDKs: (list the critical ones — frameworks, API clients, etc.)
- Dependency philosophy: (e.g., minimal, curated, framework-heavy)
- Anything explicitly forbidden: (e.g., no ORM, no jQuery, no agent frameworks)

---

## Configuration and Secrets

- How are secrets managed? (e.g., environment variables, secrets manager, vault)
- How is non-secret configuration managed? (e.g., config files, environment variables, flags)
- What must not be stored in code or config files? (e.g., API keys, credentials)

---

## State and Data

- Where does state live? (e.g., database, files on disk, external service, stateless)
- Data store technology: (e.g., PostgreSQL, SQLite, filesystem, none)
- Any data constraints: (e.g., no PII, encryption at rest, data classification)

---

## Deployment and Distribution

- How is the system distributed or deployed? (e.g., npm package, Docker image, CI/CD pipeline)
- Target environments: (e.g., macOS + Linux, cloud VMs, Kubernetes)
- Any deployment constraints: (e.g., no native dependencies, must run offline)

---

## Testing

- Test framework: (e.g., Vitest, Jest, pytest, JUnit)
- Test philosophy: (e.g., unit + integration, TDD, property-based)

---

## Integration Points

- External services or APIs: (list what the system connects to)
- Internal dependencies: (other systems or repos this depends on)

---

## Constraints and Principles

- Architectural principles: (e.g., stateless design, no shared mutable state, fail gracefully)
- Hard constraints from the PRD: (reference specific PRD non-goals or constraints that affect architecture)

---

## Completeness Checklist

Before handing this to the ACF generation prompt, confirm:

- [ ] Runtime and language are specified
- [ ] Key dependencies are listed
- [ ] Secrets handling is defined
- [ ] State management approach is clear
- [ ] Distribution/deployment method is specified
- [ ] Hard constraints from the PRD are referenced
