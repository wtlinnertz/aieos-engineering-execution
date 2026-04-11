# Security Principles

Version: v1.0

## Purpose

This document defines mandatory security controls for all production-bound software.

These controls are not suggestions.
They are enforceable requirements that integrate with artifact generation, CI gates, and runtime policy enforcement.

Security is treated as a control layer of the SDLC — not a best-practice guideline.

This document is **input material for ACF, DCF, and TDD generation** — it is not an artifact spec. The ACF translates these security requirements into system-specific architectural guardrails. The DCF translates them into implementation-level quality standards. The TDD translates them into required test coverage.

---

# 1. Enforcement Model

Security controls are enforced proportionally based on Work Classification:

| Work Type | Enforcement Level |
|------------|------------------|
| Bug Fix (Low Risk) | Minimal delta validation |
| Feature Enhancement | Targeted control validation |
| New Capability | Full control validation |
| Infrastructure / Platform Change | Full control validation + Architecture review |

Validators apply the gate set corresponding to the declared Work Classification. Work Classification is declared at the start of the SDLC flow and does not change mid-flow.

---

# 2. Core Secure Coding Controls

## 2.1 Input Handling

- All external inputs must be validated.
- Input must be constrained by type, length, and allowed values.
- Reject by default — never trust client-side validation.
- Static analysis must be applied where tooling is available.
- All new API endpoints require server-side input validation logic.

## 2.2 Authentication & Authorization

- No custom authentication mechanisms.
- Use platform-standard identity providers.
- Authorization must be explicit and deny-by-default.
- Role or claim checks must be enforced server-side.
- All new endpoints must declare their authorization model. Undefined access control is non-compliant.

## 2.3 Secret Management

- Secrets must never be hardcoded.
- Secrets must not exist in source control.
- Use platform-managed secret stores.
- Environment variables must reference secure stores, not contain secret values directly.
- Secret scanning in CI is mandatory. Detected secrets block merge.

## 2.4 Dependency & Supply Chain Security

- All dependencies must be version-pinned and actively maintained.
- High or critical vulnerabilities block merge.
- Transitive dependencies are not exempt.
- Automated dependency scanning is required on all production-bound builds.
- High severity vulnerabilities require formal risk acceptance if not remediated.

## 2.5 Error Handling

- Stack traces must not be exposed to external consumers.
- Infrastructure details must not be leaked in error responses.
- Error responses must be sanitized before reaching external consumers.
- Production profiles must disable debug output.

## 2.6 Logging & Sensitive Data

- Never log passwords, tokens, secrets, or full PII records.
- Logs must support traceability without leaking sensitive information.
- New authentication flows require logging review before release.
- Automated secret pattern detection in logs is required where feasible.

## 2.7 Cryptography

- Custom cryptographic algorithms are prohibited.
- Use platform-approved libraries only.
- Use modern protocols (TLS 1.2+ minimum).
- Hash passwords using strong adaptive hashing algorithms.
- Use of deprecated algorithms is non-compliant. Custom crypto implementations require an architecture exception.

## 2.8 Secure Configuration

- Secure defaults must be enabled.
- Debug mode must be disabled in production.
- Feature flags must not bypass security controls.
- Security-relevant configuration must be environment-specific and validated in CI.

---

# 3. CI/CD Security Gates

The following automated controls are mandatory for production-bound code:

- SAST scanning
- Dependency vulnerability scanning
- Secret scanning
- Container image scanning (if applicable)
- IaC scanning (if applicable)

Pipelines must fail on:
- Critical vulnerabilities
- Detected secrets
- Disallowed configurations

No manual override without a formal exception record.

---

# 4. Runtime Security Alignment

This section defines the security properties that must hold for all deployed workloads. The Architecture Constraint File translates these requirements into system-specific architectural guardrails — it is the appropriate place to specify how these properties are implemented for a given system.

- Workloads must run with least privilege.
- Privileged containers require explicit architectural approval.
- Network access must be restricted to declared dependencies.
- Security context must be explicitly defined for all workloads.

Platform policy enforcement (e.g., admission controllers) is preferred over developer discretion.

---

# 5. Exceptions

Security exceptions must:

- Be documented with a unique exception record.
- Include a risk assessment.
- Include an expiration date.
- Be approved by a designated authority.

Exceptions are temporary by default. Undocumented exceptions are non-compliant.

---

# 6. Non-Negotiable Principles

- Security is deny-by-default.
- Security must be automated wherever possible.
- Security guidance that cannot be validated should not exist.
- Security controls scale with risk — proportional to declared Work Classification.
- Production software without enforced security gates is non-compliant.

---

# 7. Relationship to Other Artifacts

| This principle section | Feeds into |
|------------------------|-----------|
| §1 Enforcement Model | Validators (gate set selection by Work Classification) |
| §2.1 Input Handling | ACF security constraints; DCF implementation standards |
| §2.2 Auth & Authz | ACF security constraints; TDD security test coverage |
| §2.3 Secret Management | ACF security constraints; CI policy |
| §2.4 Dependency Security | ACF dependency policy; CI policy |
| §2.5 Error Handling | DCF quality bars; TDD error scenario coverage |
| §2.6 Logging | DCF quality bars; TDD security test scenarios |
| §2.7 Cryptography | ACF security constraints; ACF forbidden patterns |
| §2.8 Secure Configuration | ACF security constraints; CI policy |
| §3 CI/CD Gates | ACF CI policy section |
| §4 Runtime Security | ACF runtime constraints (implementation via ACF) |
| §5 Exceptions | Exception record in consuming project (follows DN pattern) |
| §6 Non-Negotiable Principles | ACF + DCF + code-prompt.md + review-prompt.md |
