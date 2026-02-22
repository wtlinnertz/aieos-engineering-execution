# DCF Specification

The DCF defines design standards and expectations that constrain TDD creation. It is reusable across many initiatives and enforces quality bars, not specific designs.

## Upstream Dependencies

- Engineering standards, testing expectations, or operational requirements (if provided)

## Required Sections

1. Document Control
2. Purpose
3. Design Principles (Hard)
4. Quality Bars (Hard)
5. Non-Goals Enforcement (Hard)
6. Operational Expectations (Hard)
7. Testing Expectations (Hard) — including Evidence Management
8. Documentation Expectations (Hard)
9. Open Items
10. Freeze Declaration

## Content Rules

### Document Control
**Rules**
- DCF ID must be present
- Owner must be identified
- Status must be explicit (Draft | Approved | Frozen)
- Applicability scope must be defined (e.g., "All delivery designs", "This platform only")

**Failure Examples**
- Missing DCF ID
- No applicability scope — unclear what TDDs this applies to

### Purpose
**Rules**
- Purpose section must exist
- Purpose must describe what design standards the DCF enforces
- Purpose must not describe a specific design or implementation

**Failure Examples**
- "This DCF describes our service implementation approach" — describes design, not standards
- Purpose section empty

### Design Principles
**Rules**
- At least one design principle must be defined
- Principles must be actionable constraints, not aspirational statements
- No implementation details (specific tools, configs, commands)

**Failure Examples**
- "Write clean code" — aspirational, not actionable
- "Use Spring Boot for all services" — implementation detail, not a principle
- No principles defined

### Quality Bars
**Rules**
- At least one quality bar must be defined
- Quality bars must be measurable or verifiable
- Bars must be constraints on TDD content, not suggestions

**Failure Examples**
- "TDD should be well-written" — not measurable
- "Consider adding tests" — suggestion, not a constraint

### Non-Goals Enforcement
**Rules**
- Non-goals enforcement section must be present
- Rules must prevent scope expansion in TDD
- Rules must be concrete and enforceable

**Failure Examples**
- "Try to stay in scope" — not enforceable
- Section missing entirely

### Operational Expectations
**Rules**
- Deployment verification expectations must be stated
- Monitoring/alerting expectations must be stated
- Auditability expectations must be stated

**Failure Examples**
- Only deployment mentioned, no monitoring or auditability
- "Ops team will handle monitoring" — not a verifiable expectation

### Testing Expectations
**Rules**
- Required test layers must be defined (e.g., unit, integration, end-to-end)
- Evidence requirements must be defined (logs, reports, artifacts)
- Promotion gates must be defined (what blocks progression)

**Failure Examples**
- "Write tests" — no specific layers or evidence requirements
- No promotion gates defined

### Documentation Expectations
**Rules**
- Required TDD sections must be listed
- Required diagram types must be listed (if any)
- Required traceability markers must be listed

**Failure Examples**
- "Document everything" — not specific
- No mention of what TDD sections are required

### Standard Enforceability
**Rules**
- All hard-marked standards must be testable or verifiable
- No aspirational language in hard constraints (e.g., "strive for", "aim to")
- Standards must be specific enough to validate against in a TDD

**Failure Examples**
- "Aim for comprehensive testing" — aspirational, not verifiable
- Standard so vague it cannot be checked in a TDD review

## Format Requirements

- Hard constraints must be clearly marked — sections labeled "(Hard)" indicate mandatory standards
- Evidence management subsection should define formats, storage, retention, and accessibility

## Completeness Rules

- All required sections must be present
- At least one design principle and one quality bar must exist
- Testing expectations must cover layers, evidence, and promotion gates
- Operational expectations must cover deployment, monitoring, and auditability

## Relationship Rules

- DCF defines standards only — it must not design solutions or reference specific implementations
- DCF constrains TDDs; every TDD must comply with the frozen DCF
- DCF is reusable across multiple initiatives within its stated applicability scope
- Non-goals enforcement rules apply to all downstream TDDs — "helpful" scope expansion is not allowed

## Hard Gates

1. **document_control** — DCF ID, owner, status, and applicability scope present
2. **purpose** — Purpose describes design standards, not specific designs
3. **design_principles** — At least one actionable principle (not aspirational)
4. **quality_bars** — At least one measurable/verifiable quality bar
5. **non_goals_enforcement** — Concrete, enforceable rules preventing scope expansion
6. **operational_expectations** — Deployment, monitoring, and auditability expectations stated
7. **testing_expectations** — Test layers, evidence requirements, and promotion gates defined
8. **documentation_expectations** — Required TDD sections, diagrams, and traceability markers listed
9. **standard_enforceability** — All hard standards testable, no aspirational language
