# PRD Specification

The PRD defines the problem, goals, scope, and success criteria for a product initiative. It must be clear enough to drive architecture without containing solutions.

## Upstream Dependencies

- Product Brief (intake form) or equivalent problem description

## Required Sections

1. Document Control
2. Problem Statement
3. Goals (What "Success" Means)
4. Non-Goals (Hard Exclusions)
5. Users / Personas
6. Requirements (Functional and Non-Functional)
7. Constraints (Hard Guardrails)
8. Assumptions
9. Out of Scope by Default
10. Open Questions
11. Acceptance / Success Criteria
12. Freeze Declaration

## Content Rules

### Problem Statement
**Rules**
- Must contain a clear problem statement
- Must identify who experiences the problem (users or personas)
- Must include rationale ("why now")

**Failure Examples**
- "We need a new system" — no problem identified, no users, no rationale
- Describing a solution instead of a problem

### Goals & Success Criteria
**Rules**
- Goals must be explicit and stated as measurable outcomes
- Success criteria must be measurable or objectively verifiable

**Failure Examples**
- "Improve user experience" — not measurable
- Goals without any way to determine if they were met

### Scope & Non-Goals
**Rules**
- In-scope functionality must be clearly defined
- Non-goals must be explicitly listed as hard exclusions
- No implied scope — anything not listed in §2 and §5 is out of scope by default

**Failure Examples**
- Non-goals section left empty with no justification
- Scope described in vague terms ("handle most user needs")

### Requirements
**Rules**
- Functional requirements must be explicitly stated
- Non-functional requirements must be explicitly stated (performance, reliability, compliance, etc.)
- Requirements must not contain implementation details or solution design

**Failure Examples**
- "Use Redis for caching" — this is a solution, not a requirement
- NFR section missing entirely

### Constraints & Assumptions
**Rules**
- Constraints must be documented (regulatory, technical, delivery)
- Assumptions must be documented with note that if false, they change scope or direction

**Failure Examples**
- No constraints listed and no "None applicable" statement
- Assumptions that are actually requirements in disguise

### Readiness
**Rules**
- No unresolved critical questions that would block architecture
- PRD must be internally consistent (goals, scope, requirements, and non-goals do not contradict)

**Failure Examples**
- Open questions that fundamentally change scope if answered differently
- A non-goal that contradicts a stated requirement

## Format Requirements

- Functional requirements should use "The system SHALL ..." language
- Each requirement should have a unique identifier (FR-1, NFR-1, etc.)

## Completeness Rules

- All required sections must be present
- Problem statement must answer what, who, and why
- At least one functional requirement and one non-functional requirement must exist
- Non-goals must be present (empty non-goals section is a failure unless justified)
- Open questions section must exist (may be empty if all questions are resolved)

## Relationship Rules

- PRD must not contain solution design or architecture
- PRD must not reference implementation details
- PRD defines intent that downstream artifacts (SAD, TDD) must not reinterpret or expand
- Non-goals are enforceable constraints on all downstream artifacts

## Hard Gates

1. **problem_definition** — Clear problem statement with identified users and rationale
2. **goals** — Explicit goals with measurable success criteria
3. **scope** — In-scope clearly defined, explicit non-goals, no implied scope
4. **requirements** — Functional and non-functional requirements explicitly stated
5. **constraints** — Constraints and assumptions documented
6. **readiness** — No unresolved critical questions, internally consistent
