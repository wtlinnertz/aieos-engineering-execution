# ORD Specification (Operational Readiness Document)

The ORD must verify that operational requirements defined in upstream artifacts (TDD, ACF, DCF) are implemented and working — gathering concrete evidence from a deployed system to confirm production readiness.

## Upstream Dependencies

- Frozen TDD (§5 Build and Deployment, §6 Failure Handling, §7 Observability, §9 Operational Notes)
- Frozen ACF (§3 Security Guardrails, §5 Reliability Guardrails, §6 Observability Guardrails)
- Frozen DCF (§5 Operational Expectations)

## Required Sections

1. Document Control
2. Scope
3. Evidence Standards
4. Deployment Verification
5. Observability Verification
6. Alerting and Monitoring
7. Failure and Rollback Verification
8. Security Verification
9. Runbook Verification
10. Open Items
11. Readiness Declaration

## Content Rules

### Scope (§1)

**Rules**
- Must restate scope from TDD §1
- Must not expand scope

**Failure Examples**
- New scope items not in TDD

### Evidence Standards (§2)

**Rules**
- Every evidence item in the document must be:
  - **Concrete** — An artifact (log, report, screenshot, test output), not an assertion
  - **Timestamped** — When the evidence was collected
  - **Traceable** — Links to the specific upstream requirement it satisfies
  - **Retrievable** — Includes a location (URL, path, or system reference)
- Evidence format, storage, and retention defined per DCF §6 (or team convention if DCF doesn't specify)

**Failure Examples**
- "This works" or "we tested this" (assertion, not evidence)
- Evidence without timestamp
- Evidence that doesn't reference upstream requirement
- Evidence without location/path

### Deployment Verification (§3)

**Rules**
- Every deployment step from TDD §5 must have a corresponding verification item
- Each item must have concrete evidence (logs, screenshots, or test results)
- Items not verified must be listed as open items

**Failure Examples**
- TDD deployment step with no verification item
- Verification item with blank or pending evidence (and not in Open Items)

### Observability Verification (§4)

**Rules**
- Every observability requirement from TDD §7 and ACF §6 must have a verification item
- Each item must have concrete evidence (log sample, metric dashboard, trace screenshot)
- Items not verified must be listed as open items

**Failure Examples**
- Missing verification for a TDD §7 requirement
- "Logging is configured" without evidence

### Alerting and Monitoring (§5)

**Rules**
- Every monitoring/alerting expectation from DCF §5 must have a verification item
- Each item must have evidence of configuration and testing (alert definition, test fire result)
- Items not verified must be listed as open items

**Failure Examples**
- DCF monitoring expectation with no verification item
- Alert defined but not test-fired

### Failure and Rollback Verification (§6)

**Rules**
- Every failure mode from TDD §6 must have a verification item
- Each item must have evidence of testing (test result, simulation log)
- Rollback behavior must be verified through testing, not just documented

**Failure Examples**
- "Rollback procedure documented" without testing evidence
- Failure mode with no verification

### Security Verification (§7)

**Rules**
- Every security guardrail from ACF §3 must have a verification item
- Each item must have evidence of how it was verified (code review, test, scan)
- If ACF defines security review triggers and any were matched, review evidence must be present
- Items not verified must be listed as open items

**Failure Examples**
- ACF guardrail with no verification
- Security review trigger matched but no review evidence

### Runbook Verification (§8)

**Rules**
- Deploy, verify, and rollback procedures must be documented
- Ownership/on-call expectations must be documented
- Procedures must have been tested (not just written)

**Failure Examples**
- Procedures written but never executed
- Missing rollback procedure

### Open Items (§9)

**Rules**
- Every unverified item must be listed with owner, deadline, and production-blocking status
- No open items with "Blocks Production? = Yes" may remain for a PASS

**Failure Examples**
- Unverified item not in open items table
- Blocking open item without resolution

## Generation Rules

When creating an ORD from upstream artifacts, follow this mapping:

1. For each deployment step in TDD §5 → create a deployment verification item
2. For each observability requirement in TDD §7 and ACF §6 → create an observability verification item
3. For each monitoring/alerting expectation in DCF §5 → create an alerting verification item
4. For each failure mode in TDD §6 → create a failure/rollback verification item
5. For each security guardrail in ACF §3 → create a security verification item
6. For each operational procedure in TDD §9 → create a runbook verification item
7. If an upstream requirement is vague, flag it as an open item rather than guessing

## Format Requirements

- Each verification item follows the template structure: Requirement, Evidence Type, Evidence, Status
- Status values: Verified | Not Verified | N/A
- Evidence must be concrete artifacts, not assertions
- Open items table format: Item | Owner | Deadline | Blocks Production?

## Completeness Rules

- Every upstream requirement in TDD §5-§7, §9, ACF §3, §5-§6, and DCF §5 has a corresponding verification item
- All verification items have concrete evidence or are listed as open items
- All evidence meets the four evidence standards (concrete, timestamped, traceable, retrievable)
- No production-blocking open items remain

## Relationship Rules

- Every verification item must trace to a specific upstream requirement (TDD, ACF, or DCF section)
- No new requirements may be introduced — the ORD only verifies what upstream artifacts defined
- Scope must not expand beyond TDD §1

## Hard Gates

1. **deployment_verification** — Every TDD §5 step has a verification item with concrete evidence
2. **observability_verification** — Every TDD §7 / ACF §6 requirement has a verification item with evidence
3. **alerting_verification** — Every DCF §5 expectation has a verification item with evidence
4. **failure_handling_verification** — Every TDD §6 failure mode has verification evidence; rollback tested
5. **security_verification** — Every ACF §3 guardrail has verification evidence; review triggers addressed
6. **runbook_verification** — Procedures documented and tested per TDD §9
7. **no_open_blockers** — No open items blocking production
8. **evidence_quality** — All evidence is concrete, timestamped, traceable, and retrievable

## What This Spec Does NOT Cover

- AI generation behavior (see `ord-prompt.md`)
- Pass/fail judgment procedure (see `ord-validator.md`)
