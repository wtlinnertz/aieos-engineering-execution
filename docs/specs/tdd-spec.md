# TDD Specification (Technical Design Document)

The TDD must define how the system will be built, tested, deployed, and operated — at a level sufficient for work decomposition into atomic, executable items without remaining design decisions.

## Upstream Dependencies

- Frozen SAD
- Frozen DCF

## Required Sections

1. Document Control
2. Intent Summary
3. Scope and Non-Goals
4. Technical Overview
5. Interfaces and Contracts
6. Build and Deployment Approach
7. Failure Handling and Rollback
8. Observability
9. Testing Strategy
10. Operational Notes
11. Dependencies
12. Risks and Assumptions
13. Freeze Declaration

## Content Rules

### Intent Summary (§1)

**Rules**
- Must restate upstream intent and scope from the SAD
- Must be consistent with SAD — no expansion
- Must not add requirements or design

**Failure Examples**
- Intent that expands beyond SAD scope
- New requirements introduced
- Missing intent summary

### Scope and Non-Goals (§2)

**Rules**
- Scope must be explicitly defined
- Non-goals must be explicitly restated from SAD
- No violation of non-goals anywhere in the document

**Failure Examples**
- Missing non-goals
- Design that contradicts a stated non-goal

### Technical Overview (§3)

**Rules**
- Must identify all major components from the SAD with their technical role
- Each component must specify: language/framework, key libraries or patterns, and how it maps to the SAD architecture
- When the system has multiple distinct technology layers (e.g., frontend, API, data access, infrastructure), each layer must have its own technical context
- Key data flows between components must be described
- Must be concrete enough that an implementer knows what technology stack applies to each component without consulting external sources

**Failure Examples**
- Single narrative paragraph for a multi-layer system with no per-component breakdown
- Component listed without language/framework ("API service" with no technology context)
- Technology choices that contradict ACF-approved patterns
- Missing component that exists in the SAD

### Interfaces and Contracts (§4)

**Rules**
- Every interface must be explicitly defined
- Each interface must specify: inputs (schemas/parameters), outputs (schemas/results), error modes
- Each interface must identify which SAD component or boundary it belongs to
- External-facing interfaces (crossing the system boundary) must declare backward compatibility expectations
- Versioning expectations defined where applicable
- Interfaces must be concrete enough to implement without interpretation

**Failure Examples**
- Interface with undefined inputs or outputs
- Missing error modes
- Vague interface description ("handles requests")
- Interface with no reference to a SAD component or boundary
- Public API with no backward compatibility statement

### Build and Deployment (§5)

**Rules**
- Build steps must be defined at a level where an engineer/AI would not need to ask questions
- Deployment steps must be defined at the same level of determinism
- Required configuration inputs must be listed
- Required secrets must be listed (names only, no values)
- Steps must be deterministic — no ambiguity about what to do

**Failure Examples**
- "Deploy to cloud" without specifying how
- Missing configuration inputs
- Steps that require interpretation or decision-making

### Failure Handling and Rollback (§6)

**Rules**
- Failure modes must be defined
- Detection signals must be specified for each failure mode
- Rollback or compensation behavior must be defined
- Partial failure behavior must be addressed

**Failure Examples**
- Missing failure modes
- Rollback described as "revert changes" without specifics
- No detection mechanism defined

### Observability (§7)

**Rules**
- Must define what must be observable (logs, metrics, traces)
- Evidence required to prove success must be specified

**Failure Examples**
- "Add logging" without specifying what
- Missing success evidence requirements

### Testing Strategy (§8)

**Rules**
- Test types must be defined (unit, integration, system/acceptance)
- Pass/fail criteria must be defined
- At least one failure test must be defined
- Testing approach must be concrete enough to derive test specifications

**Failure Examples**
- "Add tests" without specifying types
- No pass/fail criteria
- No failure test defined

### Operational Notes (§9)

**Rules**
- Deploy, verify, and rollback procedures must be documented
- Ownership/on-call expectations must be documented (at least generically)

**Failure Examples**
- Missing rollback procedure
- No verification procedure

### Readiness

**Rules**
- No unresolved decisions may remain
- Design must be deterministic — an implementer can build from this without asking questions

**Failure Examples**
- "TBD" entries in design sections
- Open questions about architecture or approach

## Format Requirements

- Interfaces use the structured format: Name, Inputs, Outputs, Error modes, Versioning
- Failure modes use structured format: Mode, Detection, Rollback/Compensation
- No implementation code in the TDD (it defines contracts, not implementations)

## Completeness Rules

- All SAD components have corresponding technical design
- All interfaces have complete contracts (inputs, outputs, error modes)
- Build and deployment steps are deterministic
- All failure modes have detection and rollback
- Testing strategy covers unit, integration, and system levels
- No unresolved decisions remain

## Relationship Rules

- Intent Summary must be consistent with SAD (no expansion)
- Scope must not expand beyond SAD
- Non-goals from SAD must be explicitly restated
- DCF standards must be respected (testing expectations, operational expectations)
- All components must trace to SAD architecture

## Hard Gates

1. **intent_alignment** — Intent Summary present and consistent with SAD; no expansion
2. **scope** — Scope explicitly defined; non-goals restated; no violations
3. **interfaces** — Interfaces explicitly defined with inputs, outputs, and error modes
4. **build_deploy** — Build and deployment steps defined and deterministic
5. **failure_handling** — Failure modes defined with rollback/compensation behavior
6. **testing** — Test types defined with pass/fail criteria; at least one failure test
7. **readiness** — No unresolved decisions; design is deterministic

## What This Spec Does NOT Cover

- AI generation behavior (see `tdd-prompt.md`)
- Pass/fail judgment procedure (see `tdd-validator.md`)
