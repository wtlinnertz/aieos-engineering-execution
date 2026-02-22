# Execution Specification

The execution phase turns a ready WDD work item into working, reviewed code through four sequential phases: Tests → Plan → Code → Review. Each phase has specific responsibilities and boundaries.

## Upstream Dependencies

- WDD work item (DoR-validated, frozen)
- TDD (frozen) — interface contracts, testing strategy, failure handling
- DCF (frozen) — testing expectations, evidence requirements, design principles (§2), quality bars (§3)
- ACF (frozen) — security guardrails (§3), compliance constraints (§4)

## Phase Sequence

Phases must execute in order. Each phase's output is an input to the next.

```
Tests → Plan → Code → Review
```

---

## Phase 1: Tests

Turn acceptance criteria into verifiable, executable test specifications.

### Content Rules

**Rules**
- Every acceptance criterion must map to at least one test
- Every acceptance criterion must have at least one failure test
- Tests must use Given/When/Then format
- No implementation code — test specifications only
- No test cases beyond what acceptance criteria require
- No assumed behavior that is not explicitly stated

**Test Categories (in order)**
1. Acceptance tests — one per WDD Given/When/Then criterion
2. Failure tests — at least one failure condition per acceptance criterion
3. Edge case tests — boundary conditions, empty inputs, invalid states
4. Regression tests — if fixing a bug, a test that fails before the fix

**Each test must include:**
- Test name (descriptive, Given/When/Then style)
- Preconditions
- Input
- Expected outcome
- Failure condition

**Test Structure Specification**
- Must specify the test file path (following project conventions)
- Must define test grouping (groups mapped to acceptance criteria or test categories)
- Phase 3 must create the test file at the specified path in the exact structure

**Failure Examples**
- Writing implementation code instead of test specs
- Adding tests for features not in the acceptance criteria
- Missing failure tests for an acceptance criterion

---

## Phase 2: Plan

Identify the minimal set of changes needed to pass approved tests while respecting TDD contracts.

### Content Rules

**Rules**
- Must list every file to be created or modified
- Must describe what changes are needed and why for each file
- Must identify interfaces to lock down (signatures, return types, data contracts)
- Must list new dependencies required (verified) or state "None"
- Must call out tradeoffs, risks, or assumptions
- Must identify sequencing if order matters
- No implementation code — plan only
- No scope expansion beyond the WDD work item
- No capabilities, features, or changes not required by the tests

**What to Lock Down**
- Method signatures (name, parameters, return type)
- Observable behavior (outputs, exceptions, status codes)
- Data contracts (JSON fields, schema shapes)
- Dependencies (libraries, versions)

**Required Plan Sections**
1. Plan Summary
2. Files to Change
3. Interfaces Locked
4. Dependencies
5. Risks and Assumptions
6. Sequencing

**Failure Examples**
- Plan that adds features not covered by any test
- Missing interface lock-down for a public API
- Including code snippets instead of describing changes

---

## Phase 3: Code

Implement the approved plan. Make approved tests pass. Nothing more.

### Content Rules

**Rules**
- Follow the approved plan exactly
- Write test code from approved test specifications before writing production code
- Make only the minimal changes needed to pass approved tests
- Touch only files identified in the approved plan
- Keep diffs small and focused — one logical change per step
- Run tests after each change
- No scope expansion beyond the WDD work item
- No features, capabilities, or changes not required by the tests
- No refactoring or improvement of code outside the plan's file list
- If anything is unclear, ask before coding — do not assume

**Implementation Order**
1. Write test code from approved test specifications — confirm tests fail
2. Write production code following the approved plan — make tests pass
3. Run all tests — confirm no regressions

**Safety Rules**
- No secrets, credentials, or sensitive data in code
- No hardcoded configuration — values from config or environment must not be embedded
- No unbounded operations — loops, queries, and retries must have explicit limits
- If a dependency doesn't exist or version differs, ask before adding

**Iteration Rules**
- Multiple iterations are normal
- If tests fail, fix the issue and re-run
- If implementation reveals scope issues, stop and report — do not expand scope

**Failure Examples**
- Expanding scope to "improve" something outside the work item
- Writing production code before tests
- Hardcoding a config value that should come from environment

---

## Phase 4: Review

Actively search for defects, scope violations, contract mismatches, and coverage gaps.

### Content Rules

**Adversarial Stance**
- Default assumption: the implementation contains problems
- Assume every code path has an unhandled edge case until proven otherwise
- Assume every interface boundary has a contract violation until verified
- Assume scope has expanded until confirmed it has not
- Assume test coverage is incomplete until verified
- Do not give the benefit of the doubt — require explicit evidence
- "Looks correct" is insufficient — state specifically what was verified and how

**Review Checks**
1. Scope adherence — changes match WDD work item scope; no additions
2. Interface compliance — implementation matches TDD contracts (signatures, return types, status codes)
3. Acceptance criteria coverage — each WDD criterion has a corresponding test
4. Failure handling — failure conditions and rollback behavior match WDD/TDD
5. Security — no secrets in code, no injection risks, access control respected, ACF §3 guardrails not violated
6. Code standards — implementation respects DCF §2 design principles and §3 quality bars
7. Test coverage — tests exist for happy path, failure conditions, and edge cases
8. Scope expansion — flag any changes beyond the WDD work item boundary

**Code Quality Checks**
9. Error handling — covers all failure modes from TDD §6
10. No hardcoded configuration
11. No unbounded operations
12. No dead code introduced by the change
13. Dependencies match approved plan
14. No duplicated logic creating maintenance risk

**Verification Checks**
15. All acceptance criterion tests pass
16. All failure condition tests pass
17. No regressions in existing tests
18. WDD Definition of Done items satisfied (PR ready, tests passing, evidence generated)
19. Rollback behavior tested or verified per WDD
20. Evidence is concrete (test reports, log output, screenshots) — not assertions

**Review Verdicts**
- PASS — implementation is correct, within scope, and verified
- CONCERNS — implementation works but has non-blocking issues
- BLOCKED — issues must be resolved before merge

**Failure Examples**
- Approving without verifying each acceptance criterion has a test
- Not checking for scope expansion
- Accepting "tests pass" without examining what the tests actually verify

## Completeness Rules

- All four phases must execute in sequence
- Each phase must complete before the next begins
- Phase outputs become inputs to subsequent phases
- Human approval is required between Phase 2 (Plan) and Phase 3 (Code)
- Human review is required after Phase 4 (Review)

## Relationship Rules

- Tests must trace to WDD acceptance criteria (not invent new requirements)
- Plan must trace to approved tests (not add capabilities beyond test coverage)
- Code must follow the approved plan (not deviate or expand)
- Review must verify against WDD scope, TDD contracts, and ACF guardrails
- Evidence generated during execution feeds the ORD
