# Design Context (DCF Intake)

A lightweight intake form for capturing design standards and expectations before generating a DCF.

Fill in what you know. Leave unknown sections blank — the DCF generation prompt will mark them as "Not provided."

**For existing codebases:** Run `codebase-analysis-prompt.md` to pre-fill this form from codebase analysis (Output B). Review the pre-filled form for accuracy before using it as DCF input.

---

## Design Principles

- What design principles does the organization enforce? (e.g., design for rollback, prefer deterministic designs, make interfaces explicit)
- Principle 1:
- Principle 2:

---

## Quality Bars

- What minimum quality expectations must every TDD meet?
- Are interfaces and contracts required to be explicit?
- Must failure and rollback behavior be defined?
- Must tests be defined with pass/fail criteria?

---

## Testing Standards

- Required test layers: (e.g., unit, integration, end-to-end)
- Test framework: (e.g., Vitest, Jest, pytest, JUnit)
- Test directory structure and naming conventions:
- Coverage expectations: (e.g., minimum percentage, critical paths only)
- Test types present in existing codebase: (unit, integration, e2e, snapshot, etc.)
- Test utilities, fixtures, or factories in use:

---

## Code Conventions

- Naming conventions: (files, variables, functions, classes)
- Error handling patterns: (e.g., try/catch, Result types, error codes)
- Logging approach: (library, structured vs. unstructured, log levels)
- Linting and formatting tools: (e.g., ESLint, Prettier, Black)

---

## Operational Expectations

- Deployment verification expectations: (e.g., health check must pass, smoke tests required)
- Monitoring/alerting expectations: (e.g., structured logging, metrics, traces)
- Auditability expectations: (e.g., evidence retention, audit trail)
- CI/CD configuration: (pipeline tools, workflow definitions)
- Environment configuration: (dev, staging, production)

---

## Evidence Management

- Required evidence formats: (e.g., structured logs, test reports, screenshots)
- Evidence storage location: (e.g., artifact repository, document management system)
- Retention requirements: (e.g., 1 year for test results, 7 years for compliance records)
- Accessibility requirements: (e.g., retrievable within 24 hours for audit)

---

## Documentation Expectations

- Required sections in a TDD: (list any mandatory sections beyond the template)
- Required diagram types: (e.g., data flow, sequence, deployment)
- Required traceability markers: (e.g., PRD requirement IDs, SAD component references)

---

## Completeness Checklist

Before handing this to the DCF generation prompt, confirm:

- [ ] At least one design principle is stated
- [ ] Quality bars are defined
- [ ] Test layers and evidence requirements are specified
- [ ] Operational expectations cover deployment, monitoring, and auditability
- [ ] Code conventions are documented (if working with an existing codebase)
