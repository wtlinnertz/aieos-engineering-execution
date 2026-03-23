# DKR Specification

Version: v1.0

The DKR defines the domain model — concepts, relationships, rules, language, and workflows — as a reusable, freezable artifact. It is architecture-agnostic: works for any domain (business, platform, data, infrastructure). It constrains SAD creation (architecture must support the domain model) and TDD creation (test scenarios must validate domain rules).

## What This Artifact Is Not

- **Not an architecture document.** The DKR defines what things ARE in the domain, not how they are implemented. Architecture decisions belong in the SAD.
- **Not a data model.** Domain entities are conceptual — they describe domain concepts, not database tables, API resources, or schema definitions.
- **Not a requirements document.** The DKR captures domain knowledge (how the domain works), not product requirements (what the system should do). Requirements belong in the PRD.

## Upstream Dependencies

- Frozen ACF (recommended — provides technology context)
- Frozen PRD or Product Brief (recommended — provides scope context)
- Optional: Capability Discovery reports, Domain Knowledge Discovery report, existing documentation

## Required Sections

1. Document Control
2. Ubiquitous Language
3. Domain Entities
4. Entity Relationships
5. Domain Rules
6. Workflows
7. Bounded Contexts

## Content Rules

### Document Control
**Rules**
- DKR ID must be present
- Owner must be identified
- Status must be explicit (Draft | Approved | Frozen)
- Applicability scope must be defined (e.g., "All initiatives in the CI/CD domain", "Payment processing services")

**Failure Examples**
- Missing DKR ID
- No applicability scope — unclear what initiatives this domain model covers

### Ubiquitous Language
**Rules**
- At least 5 terms must be defined
- Each definition must be precise and unambiguous — two people reading it should agree on the boundary
- No circular definitions (a term must not be defined using itself or a synonym)
- Synonyms-to-avoid must be listed for terms with common alternatives

**Failure Examples**
- "Pipeline — a CI/CD pipeline" — circular definition
- Fewer than 5 terms defined
- No synonyms-to-avoid column
- "Deployment — the act of deploying" — circular, uses the term to define itself

### Domain Entities
**Rules**
- Every entity referenced in §4–§6 must be defined here
- Attributes must be domain-level, not implementation-level
- Lifecycle states must be domain states (e.g., "active", "retired", "pending-approval"), not system states (e.g., "HTTP 200", "deployed", "container-running")
- Each entity must have a description and at least one key attribute

**Failure Examples**
- Entity "Pipeline" defined with attribute "container_image" — implementation detail, not domain
- Entity referenced in §5 rules but not defined in §3
- Lifecycle state "HTTP 200" — system state, not domain state
- Entity with no attributes listed

### Entity Relationships
**Rules**
- Every entity in §3 that has connections to other entities must have relationships stated
- Relationship type must be explicit (owns, references, triggers, produces, consumes, depends-on) — not just "related to"
- Cardinality must be stated (1:1, 1:N, N:M)
- Both sides of each relationship must reference entities defined in §3

**Failure Examples**
- "Pipeline relates to Environment" — no relationship type
- Missing cardinality
- Relationship references "Build Server" but no such entity in §3

### Domain Rules
**Rules**
- Every rule must be specific enough to validate in a test scenario
- No aspirational language (e.g., "should be reliable", "aim for quality")
- Each rule must reference at least one entity from §3
- Enforcement point must be stated (where in the lifecycle this rule applies)
- Rule IDs must follow the format DR-NNN

**Failure Examples**
- "Pipelines should be reliable" — aspirational, not testable
- Rule references "deployment" but no Deployment entity in §3
- Rule has no enforcement point — unclear when or where to check it
- "Follow best practices" — not specific enough to validate

### Workflows
**Rules**
- Every workflow must reference only entities and rules from §3–§5
- No implementation steps — no tool names, no commands, no API endpoints
- Workflows describe WHAT happens in domain terms, not HOW
- Each workflow must have a trigger, numbered steps, and an outcome

**Failure Examples**
- "Run terraform apply" — implementation step
- Workflow references "Build Server" but no such entity in §3
- "Call POST /api/deployments" — implementation detail
- Workflow with no trigger or no outcome defined

### Bounded Contexts
**Rules**
- Must explicitly state what is in scope and what is out of scope
- Adjacent domain interfaces must be identified
- Interaction type must be stated using standard vocabulary (shared kernel, customer-supplier, conformist, anticorruption layer, or separate ways)

**Failure Examples**
- No out-of-scope statement
- Adjacent domain listed but no interaction type
- Interaction type described as "we talk to them" — not standard vocabulary

## Format Requirements

- Ubiquitous Language must be presented as a table with columns: Term, Definition, Synonyms to Avoid, Used In
- Domain Entities must be presented as a table with columns: Entity, Description, Key Attributes, Lifecycle States
- Entity Relationships must be presented as a table with columns: From Entity, Relationship Type, To Entity, Cardinality, Description
- Domain Rules must use numbered entries with Rule ID, Rule Statement, Entities Involved, Enforcement Point
- Workflows must use numbered blocks with Name, Trigger, Steps, Outcome
- Bounded Contexts must include In Scope and Out of Scope bullet lists plus an Adjacent Domain Interfaces table

## Completeness Rules

- All 7 required sections must be present
- Ubiquitous Language must have at least 5 terms
- Every entity referenced in §4–§6 must be defined in §3
- Every relationship must have type and cardinality
- Every rule must have a rule ID and enforcement point
- Every workflow must reference only entities and rules from §3–§5
- Bounded Contexts must have both in-scope and out-of-scope statements

## Relationship Rules

- DKR defines domain concepts only — it must not prescribe implementation or architecture
- DKR constrains SAD; every SAD must account for DKR entities
- DKR constrains TDD; every TDD must validate DKR rules
- DKR is reusable across multiple initiatives within its stated applicability scope
- DKR is architecture-agnostic — the same DKR works whether the domain is implemented as microservices, monolith, IaC modules, pipelines, or any other architecture

## Hard Gates

1. **document_control** — DKR ID, owner, status, and applicability scope present
2. **ubiquitous_language** — At least 5 terms with precise, unambiguous definitions; no circular definitions; synonyms-to-avoid listed
3. **entities_complete** — Every entity referenced in §4–§6 is defined in §3; no orphan references
4. **relationships_explicit** — Every entity in §3 with connections has relationships in §4; type and cardinality stated
5. **rules_enforceable** — Every rule in §5 specific enough to validate in a test; no aspirational language; enforcement point stated
6. **workflows_traceable** — Every workflow in §6 references only entities and rules from §3–§5; no implementation steps
7. **bounded_context_defined** — §7 states in-scope and out-of-scope; adjacent domain interfaces identified with interaction type

## Artifact ID Format

DKR IDs follow the format: `DKR-{SCOPE}-{NNN}` (e.g., `DKR-CICD-001`, `DKR-PAYMENTS-001`)
