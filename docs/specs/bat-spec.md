# Business Acceptance Testing — Specification

Version: v1.0

Business Acceptance Testing (BAT) verifies that a work group's business capability works end-to-end. It is conducted after the work group gate is recorded and before proceeding to the next group or to ORD. BAT is a business-level verification — it tests whether the product owner's acceptance criteria are satisfied from a user perspective, not whether the implementation is technically correct.

BAT produces two outputs: a verification protocol (AI-generated) and a results record (human-executed). The results record is the evidence artifact.

---

## What This Artifact Is Not

- **Not a technical test plan.** BAT verifies business capabilities, not implementation correctness. Technical testing is the responsibility of Phase 1 (Tests) and QAK verification campaigns.
- **Not an implementation review.** BAT does not evaluate code quality, architecture compliance, or test coverage. That is Phase 4 (Review).
- **Not a pass/fail judgment by AI.** The AI generates the protocol. A human executes it and records the outcome.

---

## Purpose

BAT serves two roles:

1. **Business validation** — Confirms that a work group's acceptance criteria are satisfied from the product owner's perspective before the next work group begins or ORD is generated
2. **Evidence generation** — Produces a structured results record that documents what was tested, by whom, and with what outcome

---

## Upstream Dependencies

- Frozen WDD (work groups with business capabilities and acceptance criteria defined)
- Recorded work group gate for the group under test (all member items complete)
- Deployed system accessible in the target test environment

---

## Required Sections — BAT Protocol (Document 1)

1. Business Capability
2. Preconditions
3. Verification Steps
4. Rollback and Cleanup

## Required Sections — BAT Results (Document 2)

1. Document Control
2. Step Results
3. Overall Outcome
4. Evidence
5. Decision

---

## Content Rules

### Business Capability
**Rules**
- Must restate the work group's business capability exactly as defined in the WDD
- Must not rephrase, summarize, or expand the capability statement

**Failure Examples**
- Capability paraphrased or summarized instead of restated verbatim
- Additional capabilities added beyond the WDD definition

### Preconditions
**Rules**
- Work group gate for the group under test must be listed as a precondition
- System deployment and accessibility in the target test environment must be stated
- Specific data, accounts, or configuration required to execute verification steps must be listed
- Preconditions must be verifiable before testing begins — not assumptions

**Failure Examples**
- Work group gate not listed as a precondition
- "System is running" without specifying environment
- Required test data not identified

### Verification Steps
**Rules**
- Exactly one verification step per group-level acceptance criterion — no more, no fewer
- Each step must restate the acceptance criterion it verifies, exactly as written in the WDD
- Actions must be specific and unambiguous — executable without knowledge of implementation internals
- Expected outcome must describe the passing result from the user or system perspective, not from the implementation perspective
- Failure indicators must describe observable failure signals
- Steps must be numbered sequentially

**Failure Examples**
- Acceptance criterion not restated verbatim in the "Verifies" field
- Actions require knowledge of code, database, or internal APIs
- Expected outcome describes implementation behavior ("returns 200 OK") instead of user-visible behavior ("the dashboard shows the updated count")
- More verification steps than acceptance criteria (scope expansion)
- Fewer verification steps than acceptance criteria (missing coverage)

### Rollback and Cleanup
**Rules**
- Must list what must be cleaned up after testing
- Must include test data, account changes, configuration changes, and other side effects
- If no cleanup is needed, must state "No cleanup required" — section must not be blank

**Failure Examples**
- Section blank
- Test data created during steps not listed for cleanup

### Document Control (Results)
**Rules**
- Tested By must be a named individual (not a team)
- Date must be present
- Environment must be specified
- Build or commit reference must be present

**Failure Examples**
- Tested By is "the team" or blank
- Environment is "test" without specifics
- Build/commit reference absent

### Step Results (Results)
**Rules**
- Every verification step from the protocol must have a corresponding result row
- Each row must have an outcome: PASS, FAIL, or BLOCKED
- BLOCKED must include a note explaining what prevented execution

**Failure Examples**
- Protocol step with no corresponding result
- Outcome blank or "N/A"
- BLOCKED with no explanation

### Overall Outcome (Results)
**Rules**
- Must declare one of: all steps PASS, or one or more steps FAIL/BLOCKED
- Must be consistent with the step results table — if any step is FAIL or BLOCKED, overall must reflect that

**Failure Examples**
- Overall outcome says PASS but a step is FAIL
- Neither checkbox marked

### Evidence (Results)
**Rules**
- Must list test data created, screenshots or recordings, and anomalies observed
- Anomalies must be documented even if all steps passed
- If no evidence is applicable for a field, state "None" — do not leave blank

**Failure Examples**
- Section blank
- "See above" instead of specific evidence references

### Decision (Results)
**Rules**
- BAT outcome must be declared: PASS or FAIL
- Approved By must be a named individual (the person who executed or reviewed the BAT)
- Date must be present

**Failure Examples**
- Outcome absent
- Approved By blank or "TBD"

---

## Format Requirements

- Verification steps numbered sequentially starting at 1
- Each step maps to exactly one group-level acceptance criterion
- Results filed as `{nn}-wg-{n}-bat-results.md` using the next available sequence number after the work group gate file
- Acceptance criteria restated verbatim — no paraphrasing

---

## Completeness Rules

- Protocol: all acceptance criteria covered with one verification step each
- Results: all protocol steps have outcomes; decision declared; evidence present
- Both documents reference the same work group ID and name

---

## Relationship Rules

- BAT is gated by the work group gate — it cannot run until the gate is recorded
- BAT failure blocks work group completion — do not proceed to the next work group or ORD
- BAT failure triggers one of two escalation paths defined in the playbook (Path A: fix within scope; Path B: scope decision via BAT Escalation Record)
- BAT results are evidence artifacts referenced by ORD

---

## Hard Gates

### BAT Protocol (generated by BAT prompt)
1. **capability_fidelity** — Business capability restated verbatim from WDD; not paraphrased or expanded
2. **precondition_completeness** — Work group gate, environment, and required test state listed
3. **criterion_coverage** — Exactly one verification step per group-level acceptance criterion; no more, no fewer
4. **step_executability** — Actions are specific and unambiguous; executable without implementation knowledge; expected outcomes are user-visible

### BAT Results (evaluated by BAT validator)
All 4 protocol gates above, plus:
5. **result_completeness** — Every protocol step has an outcome (PASS/FAIL/BLOCKED); overall outcome consistent with step results
6. **evidence_present** — Document control fields populated; evidence section populated; decision declared with named approver
