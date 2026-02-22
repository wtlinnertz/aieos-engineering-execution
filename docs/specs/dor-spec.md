# DoR Specification (Definition of Ready)

The DoR evaluates whether a WDD work item is complete and unambiguous enough to begin execution. It is a per-item readiness gate, not a document-level validator.

## Upstream Dependencies

- WDD (frozen or validated)
- TDD (frozen) — for interface contracts and testing strategy
- DCF (frozen) — for testing expectations

## Required Properties per Work Item

Each WDD work item must satisfy all of the following properties to be considered ready for execution.

## Content Rules

### Traceability
**Rules**
- Work item must reference a WDD Item ID
- Work item must reference a TDD section

**Failure Examples**
- No WDD Item ID present
- TDD section reference missing or "TBD"

### Atomic Scope
**Rules**
- Work item must have one clear outcome
- No compound objectives (e.g., "implement X and refactor Y")

**Failure Examples**
- "Add logging and refactor error handling" — two objectives
- Outcome described in vague terms ("improve the system")

### Acceptance Criteria
**Rules**
- For AI-assigned items (Assignee Type: AI Agent or Either): Must use Given/When/Then format with at least one failure condition per acceptance criterion
- For human-assigned items (Assignee Type: Human): Must use verification checklist format (action + expected evidence) with at least one failure/rollback check
- Format must match the Assignee Type

**Failure Examples**
- AI-assigned item using a plain text checklist instead of Given/When/Then
- Human-assigned item using Given/When/Then instead of verification checklist
- Acceptance criteria with no failure condition defined
- "It should work correctly" — not a testable criterion

### Inputs and Outputs
**Rules**
- Inputs must be explicitly listed
- Outputs must be explicitly listed
- No hidden dependencies (inputs that exist but are not listed)

**Failure Examples**
- Inputs section empty or missing
- Output described as "the result" without specifics
- Code references a config file not listed as an input

### Definition of Done
**Rules**
- PR merged must be a completion criterion
- Tests passing must be specified (with test type)
- Evidence or logs must be generated

**Failure Examples**
- No Definition of Done section
- "Tests pass" without specifying which tests
- No evidence requirement

### Dependencies
**Rules**
- Dependencies must be explicitly listed or stated as "None"

**Failure Examples**
- Dependencies section missing entirely
- Dependencies section present but empty (no "None" declaration)

### Execution Ownership
**Rules**
- Assignee Type must be set to one of: AI Agent | Human | Either

**Failure Examples**
- Assignee Type missing
- Assignee Type set to a specific person's name instead of a type

### AI Safety
**Rules**
- No open-ended language that would allow unbounded execution
- No unresolved decisions (no "TBD", "to be determined", or deferred choices)
- No undocumented assumptions

**Failure Examples**
- "Implement as appropriate" — open-ended
- "Authentication approach: TBD" — unresolved decision
- Work item assumes an API exists but doesn't document this assumption

### Granularity Enforcement
**Rules**
- Single outcome per work item
- Single repo or subsystem per work item
- Single PR feasible
- No cross-environment execution

**Failure Examples**
- Work item spans two repositories
- Work item requires coordinated changes across staging and production
- Estimated scope clearly exceeds a single PR

### Rollback / Failure Behavior
**Rules**
- Failure behavior must be defined (what happens when things go wrong)
- Rollback or compensation approach must be stated
- Must not be left blank or marked TBD

**Failure Examples**
- Rollback section marked "TBD"
- No mention of what happens on failure
- "Roll back if needed" — no specific approach

## Completeness Rules

- All 10 properties must be evaluated
- A work item is ready only if all properties pass
- Missing sections are automatic failures (not warnings)

## Relationship Rules

- DoR evaluates individual WDD work items, not the WDD as a whole
- Acceptance criteria format must match the Assignee Type defined in the work item
- Inputs/outputs must be consistent with what the TDD specifies for the referenced section
- Failure behavior must align with TDD §6 (Failure Handling and Rollback)

## Hard Gates

1. **traceability** — References WDD Item ID and TDD section
2. **atomic_scope** — One clear outcome, no compound objectives
3. **acceptance_criteria** — Correct format for assignee type, failure conditions present
4. **inputs_outputs** — Inputs and outputs explicitly listed, no hidden dependencies
5. **definition_of_done** — PR merged, tests passing (type specified), evidence generated
6. **dependencies** — Explicitly listed or "None"
7. **execution_ownership** — Assignee Type set (AI Agent | Human | Either)
8. **ai_safety** — No open-ended language, no unresolved decisions, no undocumented assumptions
9. **granularity** — Single outcome, single repo, single PR, no cross-environment
10. **rollback_behavior** — Failure behavior defined, rollback approach stated, not TBD
