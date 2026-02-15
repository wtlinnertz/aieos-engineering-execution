# SAD Template (System Architecture Design)

## 0. Document Control
- System Name:
- SAD ID:
- Author:
- Date:
- Status: Draft | Approved | Frozen
- Upstream Artifacts:
  - PRD ID / Link:
  - ACF ID / Link:
- Related ADRs:

## 1. Intent Summary (Paste Approved Pre-Pass)
Paste the approved Intent Summary here verbatim.
Do not restate or expand it.

## 2. Scope and Non-Goals (Hard Boundary)

### In Scope
- Item 1:
- Item 2:

### Explicit Non-Goals
- Non-goal 1:
- Non-goal 2:

Rule: Anything not listed as in-scope is out of scope by default.

## 3. System Context (Black Box)
Describe the system as a black box.

### Responsibilities
- Responsibility 1:
- Responsibility 2:

### External Actors / Systems
- Upstream:
- Downstream:
- Users/Clients:

### Trust Boundaries
- Boundary 1:
- Boundary 2:

### Diagrams
Include a C4 Context (L1) diagram in Mermaid showing the system and its external actors.

```mermaid
graph TB
    %% C4 Context diagram here
```

## 4. High-Level Architecture (White Box)
Define major components and interactions.

### Major Components
For each component:
- Name:
- Responsibility:
- Key interactions:

### Communication Patterns
- Sync vs async:
- Protocols (high level):
- High-level data flow:

### Diagrams
Include a C4 Container (L2) diagram and a Data Flow diagram in Mermaid.

```mermaid
graph LR
    %% C4 Container diagram here
```

```mermaid
graph LR
    %% Data Flow diagram here
```

## 5. Key Architectural Decisions
Record only decisions that affect system shape.
Each decision should reference an ADR when applicable.

- Decision:
  - Rationale:
  - Alternatives considered:
  - Consequences:

## 6. Cross-Cutting Concerns (Architectural Handling)

### Security
- Authn/authz posture:
- Data protection posture:

### Reliability and Resilience
- Failure isolation strategy:
- Retry/fallback philosophy:

### Observability
- What must be observable (not how):

### Performance and Scale
- Scaling model (high level):
- Constraints (high level):

## 7. Data and Integration

### Data Stores
For each data store:
- Name:
- Ownership:
- Access pattern:

### Integration Patterns
- Pattern 1:
- Pattern 2:

### State Transitions
Describe data or artifact state transitions at the architectural level.

## 8. Failure Modes and Recovery

| Failure Mode | Impact | Detection | Mitigation |
|-------------|--------|-----------|------------|
| Mode 1 | | | |
| Mode 2 | | | |

Mitigation must be architectural, not procedural.

## 9. Quality Attribute Scenarios (QAS)

Define at least one scenario per major quality attribute.

| Quality Attribute | Scenario | Response | Measure |
|------------------|----------|----------|---------|
| Availability | | | |
| Performance | | | |
| Security | | | |

## 10. Constraints and Guardrails (from ACF)
List hard constraints that downstream artifacts must respect.
- Guardrail 1:
- Guardrail 2:

## 11. Deferred Decisions (Explicit)
List items intentionally deferred to TDD-level detail.
- Deferred 1:
- Deferred 2:

## 12. Risks and Assumptions

### Risks
- Risk 1:
- Risk 2:

### Assumptions
- Assumption 1:
- Assumption 2:

## 13. Readiness Checklist (Self-Check)
- [ ] Intent Summary pasted and unchanged
- [ ] Scope and non-goals explicit
- [ ] Boundaries and major components clear
- [ ] C4 Context (L1) diagram present
- [ ] C4 Container (L2) diagram present
- [ ] Data Flow diagram present
- [ ] Data stores and integrations documented
- [ ] Failure modes table complete
- [ ] At least one QAS per quality attribute
- [ ] Decisions resolved or explicitly deferred
- [ ] No implementation details (build steps, configs, pipelines)

## 14. Freeze Declaration (when ready)
This SAD is approved and frozen. Downstream artifacts may not reinterpret or expand this architecture.

- Approved By:
- Date:
