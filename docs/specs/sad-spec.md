# SAD Specification (System Architecture Design)

The SAD must describe the system boundary, major components, responsibilities, and interactions at the architectural level — sufficient for a downstream Technical Design Document to be produced without ambiguity.

## Upstream Dependencies

- Frozen PRD
- Frozen ACF
- ADRs (if referenced)

## Required Sections

The SAD template defines the structural contract. All required sections must be present and substantive:

1. Document Control
2. Intent Summary
3. Scope and Non-Goals
4. System Context (Black Box)
5. High-Level Architecture (White Box)
6. Key Architectural Decisions
7. Cross-Cutting Concerns
8. Data and Integration
9. Failure Modes and Recovery
10. Quality Attribute Scenarios
11. Constraints and Guardrails
12. Deferred Decisions
13. Risks and Assumptions
14. Freeze Declaration

Sections that are not applicable must be explicitly marked as such with justification. Placeholders like "TBD" or "TODO" are failures. Empty bullets or vague statements are failures.

## Content Rules

### Intent Summary (§1)

**Rules**
- Must restate upstream intent, constraints, and non-goals from the PRD
- Must be consistent with PRD intent — no scope expansion
- Must not add requirements or design
- Must not include editorial commentary

**Failure Examples**
- Missing intent summary
- Intent that contradicts or expands PRD scope
- Editorial commentary added

### Scope and Non-Goals (§2)

**Rules**
- "In Scope" section must be substantive
- "Explicit Non-Goals" section must exist with concrete entries
- Anything not listed as in-scope is out of scope by default

**Failure Examples**
- Missing non-goals
- "Out of scope for now" without explanation
- Generic placeholder non-goals

### Upstream Artifact Traceability (§0, §5, §10)

**Rules**
- PRD reference must be present
- ACF reference must be present
- ADRs must be referenced when architectural trade-offs exist
- Architectural decisions must be traceable to PRD goals, ACF guardrails, or ADRs

**Failure Examples**
- Missing PRD or ACF reference
- Trade-offs described with no ADR or explanation
- Unexplained architectural decisions

### Structural Diagrams (§3, §4)

**Rules**
- C4 Context (L1) diagram must be present
- C4 Container (L2) diagram must be present
- Data Flow diagram must be present
- All diagrams must be syntactically valid Mermaid
- Diagrams must describe structure, not execution steps
- Diagrams must have architectural relevance

**Failure Examples**
- Missing diagram
- Non-Mermaid diagram format
- Commands, configs, or code embedded in diagrams
- Execution flow depicted instead of architecture
- Invalid Mermaid syntax

### Cross-Cutting Concerns (§6)

**Rules**
- Security section must be present with architectural content
- Reliability and Resilience section must be present with architectural content
- Observability section must be present with architectural content
- Performance and Scale section must be present with architectural content
- Content must be architectural, not operational or procedural

**Failure Examples**
- "Handled by platform" with no explanation
- Tool configuration details included
- Procedural steps described instead of architecture
- Vague statements without substance
- Missing sections

### Data and Integration (§7)

**Rules**
- Data stores must be identified with ownership and access patterns
- Integration patterns must be described architecturally
- Data flows must include artifact or state transitions

**Failure Examples**
- Data flow described without state changes
- Ownership not identified
- Integration described as step-by-step process
- Tool-specific integration details

### Failure Modes and Recovery (§8)

**Rules**
- Failure modes table must be present
- Each entry must include impact, detection, and mitigation
- Mitigation must be architectural, not procedural

**Failure Examples**
- "Retry the job" without architectural context
- Missing detection mechanism
- Empty or incomplete failure mode entries

### Quality Attribute Scenarios (§9)

**Rules**
- At least one QAS per major quality attribute
- Scenarios must be concrete and testable
- Responses must describe architectural behavior

**Failure Examples**
- Vague statements ("system should be fast")
- Tool-centric responses
- Missing quality attributes

### Deferred Decisions (§11)

**Rules**
- Deferred Decisions section must be present
- Each decision must include reason and target resolution phase
- No unexplained "TBD" entries

**Failure Examples**
- Placeholder-only decisions
- No timeline for resolution
- Decisions without context or justification

### Risks and Assumptions (§12)

**Rules**
- Architecture risks must be identified
- Risks must include impact and mitigation
- Mitigation must be architectural, not procedural

**Failure Examples**
- Empty risk table
- Risks described without mitigation
- Procedural mitigations only
- Vague risk descriptions

### Guardrail Alignment (§10)

**Rules**
- Explicit alignment with ACF must be described
- Exceptions must be called out and justified
- ADRs must be referenced for exceptions
- Content must be architectural, not procedural

**Failure Examples**
- Guardrails ignored
- Exceptions not documented
- No ADRs for exceptions
- Vague statements of alignment

## Format Requirements

- All diagrams must use Mermaid syntax
- Failure Modes must use the table format: Failure Mode | Impact | Detection | Mitigation
- Quality Attribute Scenarios must use the table format: Quality Attribute | Scenario | Response | Measure
- Content must be architectural — no code blocks, configuration snippets, or step-by-step execution flows

## Completeness Rules

- All required sections present and substantive (no placeholders)
- All three required diagrams present and valid
- At least one QAS per major quality attribute
- All failure modes have complete entries (impact + detection + mitigation)
- All deferred decisions have reason and target resolution phase
- All architectural decisions traceable to upstream artifacts
- Generic statements like "Handled by platform" must include explanation

## Relationship Rules

- Intent Summary must be consistent with PRD (no expansion, no contradiction)
- Scope must not expand beyond PRD
- Non-goals from PRD must be explicitly restated
- ACF guardrails must be respected; exceptions must be justified and documented
- Architectural decisions must be traceable to PRD, ACF, or ADRs

## Hard Gates

1. **intent_integrity** — Intent Summary is consistent with PRD; no scope expansion
2. **scope_discipline** — In Scope and Non-Goals sections are substantive and concrete
3. **upstream_traceability** — PRD, ACF, and ADR references are present and traceable
4. **structural_diagrams** — All three required diagrams are present, valid Mermaid, and describe structure (not execution)
5. **cross_cutting_concerns** — Security, Reliability, Observability, and Performance sections have architectural content
6. **data_integration** — Data stores have ownership; integration patterns are architectural; data flows include state transitions
7. **failure_modes** — Failure modes table is complete; mitigations are architectural
8. **quality_attributes** — QAS scenarios are concrete and testable; responses describe architectural behavior
9. **deferred_decisions** — Each deferred decision has reason and target resolution phase
10. **risk_awareness** — Risks identified with impact and architectural mitigation
11. **guardrail_alignment** — ACF alignment explicit; exceptions justified with ADRs
12. **implementation_leakage** — No code blocks, configs, or procedural step-by-step flows

## Completeness Score

Score range: 0–100. Calculated as the percentage of required sections that are present, substantive, not placeholders, free of implementation leakage, and traceable to upstream artifacts.

## What This Spec Does NOT Cover

- Judging architectural quality or elegance
- Comparing architectures
- Enforcing technology choices
- Validating performance numbers
- AI generation behavior (see `sad-prompt.md`)
- Pass/fail judgment procedure (see `sad-validator.md`)
