# WDD Specification (Work Design Document)

The WDD must decompose a frozen TDD into atomic, executable work items grouped into business-testable work groups — sufficient for AI agents or humans to execute without design decisions.

## Upstream Dependencies

- Frozen TDD

## Required Sections

1. Document Control
2. Scope and Non-Goals (copied from TDD)
3. Work Items (repeated per item)
4. Work Groups
5. Freeze Declaration

## Content Rules

### Scope and Non-Goals (§1)

**Rules**
- Scope must be copied verbatim from TDD (or link to exact section)
- No scope expansion beyond TDD
- Non-goals must align with TDD

**Failure Examples**
- Restated or paraphrased scope
- New scope items not in TDD

### Work Items (§2)

**Rules**
- Each work item must have: Intent, In Scope, Out of Scope, Inputs, Outputs, Acceptance Criteria, Definition of Done, Dependencies, Rollback/Failure Behavior, Required Capabilities
- Intent must be 1–2 sentences describing exactly what the item accomplishes
- Inputs and outputs must be explicitly listed
- Dependencies must be explicitly listed or stated as "None"
- Rollback/failure behavior must be defined, not blank or TBD

**Failure Examples**
- Missing required fields
- Vague intent ("implement the feature")
- Dependencies listed as TBD
- Empty rollback section

### Acceptance Criteria

**Rules**
- AI-assigned items (Assignee Type: AI Agent or Either): Must use Given/When/Then format
- Human-assigned items (Assignee Type: Human): Must use verification checklist format (action + expected evidence)
- At least one failure condition per acceptance criterion
- Criteria must be executable — testable without interpretation

**Failure Examples**
- Acceptance criteria without failure conditions
- Given/When/Then on a human-assigned item
- Verification checklist on an AI-assigned item
- Vague criteria ("works correctly")

### Definition of Done

**Rules**
- Must include: PR merged, tests passing (type specified), evidence/logs generated
- Test type must be specified (unit, integration, etc.)

**Failure Examples**
- Generic "tests pass" without type
- Missing evidence requirement

### Assignee Type

**Rules**
- Every work item must have Assignee Type: AI Agent | Human | Either
- Items covering packaging, distribution, deployment verification, infrastructure provisioning, or release tasks should be assigned Assignee Type "Human"
- Human-assigned items use verification checklists instead of Given/When/Then
- Human-assigned items follow verification checklist execution model (not the four-phase AI loop)
- Human-assigned items must still have explicit inputs, outputs, Definition of Done, and rollback behavior

**Failure Examples**
- Missing Assignee Type
- Deployment item assigned to AI Agent
- Human item with Given/When/Then acceptance criteria

### Required Capabilities

**Rules**
- Every work item must list at least one required capability
- Capabilities describe technical skill domains, not roles or people
- Capabilities must be inferred from the work item's subsystem, inputs, outputs, and TDD contracts
- Use short, recognizable domain labels (e.g., `backend`, `frontend`, `database`, `infrastructure`, `API-design`, `security`, `observability`)
- Do not reference team names, individual names, or job titles

**Failure Examples**
- Empty or missing capabilities list
- Role-based labels ("Senior Engineer", "Tech Lead")
- Overly broad labels that apply to every item ("engineering", "development")

### Complexity Estimate

**Rules**
- Every work item must include a Complexity Estimate: S | M | L
- Complexity is a relative sizing signal for planning, not a commitment or deadline
- The estimate must be accompanied by a one-sentence justification referencing the sizing factors

**Sizing Definitions**

| Size | Label | Characteristics |
|------|-------|----------------|
| **S** | Small | Single concern; no dependencies on other work items; straightforward implementation with well-understood patterns; ≤ 2 acceptance criteria |
| **M** | Medium | Multiple concerns or one dependency on another work item; moderate implementation requiring some integration; 3–4 acceptance criteria |
| **L** | Large | Cross-cutting concerns, multiple dependencies, or unfamiliar/complex domain; significant integration or coordination required; ≥ 5 acceptance criteria |

**Sizing Factors (used to determine S/M/L)**
1. **Scope indicator count** — Number of In Scope bullets (more bullets → higher complexity)
2. **Acceptance criteria count** — Number of ACs including failure conditions (more criteria → more testing surface)
3. **Dependency count** — Number of work item dependencies (more dependencies → more coordination)
4. **Capability breadth** — Number of distinct Required Capabilities (more domains → more expertise needed)
5. **Integration surface** — Whether the item touches external systems, APIs, or infrastructure boundaries
6. **Novelty** — Whether the item uses well-established patterns or requires unfamiliar approaches

**Important Limitations**
- These estimates reflect structural complexity visible in the WDD, not actual effort hours
- The AI has no knowledge of team velocity, codebase state, or individual skill levels
- Teams must calibrate S/M/L to their own velocity data (e.g., "S = half day, M = 1-2 days, L = 3-5 days" — defined per team, not per kit)
- Estimates are advisory — they do not gate validation or freeze

**Failure Examples**
- Missing complexity estimate
- Estimate without justification
- Justification referencing team-specific context the AI cannot know ("this is easy for our team")
- Using numeric hour estimates instead of S/M/L

### Work Groups (§3)

**Rules**
- Every work item must belong to exactly one group
- Each group must have: Group ID, Group Name, Business Capability statement, Member Items list, Group-Level Acceptance Criteria
- Groups must deliver a business-testable capability
- Items that are independently business-testable may form a single-item group
- Groups are organizational labels — they do not change item scope, granularity, or execution order
- No group should require the entire TDD to be complete before it is testable

**Failure Examples**
- Work item not assigned to any group
- Work item assigned to multiple groups
- Group without business capability statement
- Group without group-level acceptance criteria
- Group that requires all TDD work to be complete

## Format Requirements

- Acceptance criteria for AI items: Given/When/Then format
- Acceptance criteria for Human items: Verification checklist format
- WDD Item ID format: `WDD-{PROJECT}-{NNN}`
- Each work item uses the repeating section structure from the template

## Completeness Rules

- All TDD scope is covered by work items (no gaps)
- Every work item satisfies all granularity rules
- Every work item has all required fields populated
- Every work item belongs to a work group
- Every work group has business-level acceptance criteria

## Relationship Rules

- Scope must match TDD exactly (no expansion, no reduction)
- Non-goals must align with TDD non-goals
- Parent TDD must be referenced and frozen
- No design decisions may remain in work items

## Granularity Rules (Hard)

Every WDD item must satisfy all of the following:

- One observable outcome
- One pull request
- One subsystem or repo
- No design decisions
- No cross-environment deployment
- Completable in a short, bounded time window

Items violating these rules must be split. See the Splitting Guidance section in the playbook for patterns.

## Hard Gates

1. **traceability** — Parent TDD referenced and frozen
2. **scope** — Scope copied from TDD; no expansion
3. **atomicity** — One outcome per work item; no bundled work
4. **inputs_outputs** — Inputs and outputs explicitly listed for each item
5. **acceptance_criteria** — Executable acceptance criteria with failure behavior; format matches assignee type
6. **granularity** — Single PR, single subsystem, no cross-environment execution
7. **readiness** — No design decisions remain; dependencies explicitly listed
8. **work_groups** — Every item in one group; each group has business capability and group-level acceptance criteria

## What This Spec Does NOT Cover

- Per-item readiness validation (see `dor-spec.md`)
- AI generation behavior (see `wdd-prompt.md`)
- Pass/fail judgment procedure (see `wdd-validator.md`)
