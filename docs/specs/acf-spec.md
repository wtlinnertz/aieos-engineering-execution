# ACF Specification

The ACF defines architecture guardrails that constrain the SAD and all downstream artifacts. It captures constraints and forbidden patterns, not solutions.

## Upstream Dependencies

- Architecture Context (intake form) or equivalent organizational standards, platform assumptions, regulatory/security constraints

## Required Sections

1. Document Control
2. Purpose
3. Platform Assumptions (High Level)
4. Security Guardrails (Hard) — including Security Review Triggers
5. Compliance / Regulatory Constraints (Hard)
6. Reliability & Resilience Guardrails (Hard)
7. Observability Guardrails (Hard)
8. Approved Architectural Patterns
9. Forbidden Patterns (Hard)
10. Standard Interfaces / Integrations (Optional)
11. Open Items
12. Freeze Declaration

## Content Rules

### Document Control
**Rules**
- ACF ID must be present
- Owner must be identified
- Status must be explicit (Draft | Approved | Frozen)
- Applicability scope must be defined (e.g., "All services", "This domain only")

**Failure Examples**
- Missing ACF ID
- Status not one of the allowed values

### Purpose
**Rules**
- Purpose section must exist
- Purpose must describe what guardrails the ACF enforces
- Purpose must not describe a solution or architecture

**Failure Examples**
- "This ACF defines our microservices architecture" — describes architecture, not guardrails
- Purpose section empty

### Platform Assumptions
**Rules**
- Runtime environment(s) must be stated
- Deployment model(s) must be stated
- Networking posture must be stated (at least high level)
- Identity model must be stated (at least high level)

**Failure Examples**
- Only runtime listed, other assumptions missing
- Implementation-level detail instead of high-level posture

### Security Guardrails
**Rules**
- At least one security guardrail must be defined
- Guardrails must be constraints, not solutions
- No implementation details (specific tools, configs, commands)

**Failure Examples**
- "Use AWS KMS for encryption" — implementation detail, not a guardrail
- No security guardrails defined at all
- "Follow best practices" — not a constraint

### Compliance & Regulatory
**Rules**
- Compliance section must be present
- Constraints must be listed or explicitly marked "None applicable" with justification

**Failure Examples**
- Section missing entirely
- "None" without justification for why no compliance constraints apply

### Reliability & Resilience
**Rules**
- Availability expectations must be stated (qualitative or quantitative)
- Failure isolation expectations must be stated
- Rollback expectations must be stated

**Failure Examples**
- Only availability mentioned, no failure isolation or rollback
- "System should be reliable" — aspirational, not a constraint

### Observability
**Rules**
- Required telemetry types must be stated (logs, metrics, traces as applicable)
- Minimum operational signals must be defined

**Failure Examples**
- "Add monitoring" — no specific telemetry types
- No minimum signals defined

### Forbidden Patterns
**Rules**
- At least one forbidden pattern must be listed
- Forbidden patterns must be clear and enforceable
- No vague prohibitions

**Failure Examples**
- "Avoid bad practices" — vague, unenforceable
- Forbidden patterns section empty

### Constraint Enforceability
**Rules**
- All hard-marked constraints must be testable or verifiable
- No aspirational language in hard constraints (e.g., "strive for", "aim to")
- Guardrails must be specific enough to validate against in a SAD

**Failure Examples**
- "Aim for high availability" — aspirational, not testable
- Constraint so vague it cannot be verified in downstream artifacts

## Format Requirements

- Hard constraints must be clearly marked or placed in sections labeled "(Hard)"
- Security review triggers should be enumerated with specific conditions

## Completeness Rules

- All required sections must be present
- Platform assumptions must cover all four areas (runtime, deployment, networking, identity)
- At least one security guardrail and one forbidden pattern must exist
- Compliance section must have content or explicit "None applicable" justification

## Relationship Rules

- ACF defines guardrails only — it must not design solutions or describe system components
- ACF constrains the SAD; the SAD must demonstrate compliance with every hard guardrail
- Absence of a rule means "allowed" — be conservative about what to constrain
- ACF is reusable across multiple initiatives within its stated applicability scope

## Hard Gates

1. **document_control** — ACF ID, owner, status, and applicability scope present
2. **purpose** — Purpose describes guardrails, not solutions
3. **platform_assumptions** — Runtime, deployment, networking, and identity stated
4. **security_guardrails** — At least one guardrail defined as constraint (not solution)
5. **compliance** — Compliance constraints listed or "None applicable" with justification
6. **reliability** — Availability, failure isolation, and rollback expectations stated
7. **observability** — Required telemetry types and minimum signals defined
8. **forbidden_patterns** — At least one clear, enforceable forbidden pattern
9. **constraint_enforceability** — All hard constraints testable, no aspirational language
