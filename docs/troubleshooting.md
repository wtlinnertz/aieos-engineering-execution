# Troubleshooting Guide — Engineering Execution Kit

## How to Use This Guide

When a validator returns FAIL, find the failing gate in the table below. The Remediation column describes the specific fix required. Reopen the artifact, apply the remediation, and rerun the validator in a new session.

**Do not embed fix attempts in your validation session.** Validators and generation are separate sessions.

---

## Kit Entry Record (KER)

This is a human-completed entry gate artifact. Fill all fields directly in the template before running the validator.

| Gate | What Failure Looks Like | Typical Cause | Remediation |
|------|------------------------|---------------|-------------|
| document_control | Missing ID, date, owner, or version | Template not fully completed | Fill all required §1 fields; no field may be blank |
| classification_check | Entry path not confirmed against WCR | Path selected without consulting the upstream Work Classification Record | Reference the WCR type and confirm the selected path (A or B) matches the WCR routing decision |
| path_selected | Path A or Path B not explicitly stated | Ambiguous entry; path left to inference | State the path explicitly; Path A requires a DPRD ID, Path B requires a Product Brief ID |
| priority_on_record | Priority not documented | Priority assumed rather than stated | Record the priority classification and its source (stakeholder decision, urgency driver, or capacity assessment) |
| scope_bounded | KER scope broader than the DPRD or Product Brief scope | Scope expansion at the entry gate | Align the KER scope statement with the upstream artifact scope verbatim |

---

## Product Requirements Document (PRD)

**Path A (placed from DPRD):** If the acceptance check fails on a Path A PRD, the DPRD itself is the source of the problem — return to PIK. Do not modify the placed DPRD.

| Gate | What Failure Looks Like | Typical Cause | Remediation |
|------|------------------------|---------------|-------------|
| problem_definition | Problem definition contradicts DPRD §1 | DPRD was modified after placement | Restore the DPRD from its frozen version; do not edit the placed artifact |
| goals | Goals not traceable to DPRD goals or VH | Goals drifted or were rewritten after placement | Return to PIK; identify what needs correction in the DPRD and freeze a corrected version before re-handoff |
| scope | Scope contains items not in the DPRD | DPRD was modified or scope was added after placement | Return to PIK; do not add scope in EEK |
| requirements | Requirements contradict or extend DPRD requirements | Requirements edited after placement | Return to PIK; corrective changes must happen at the DPRD level |
| constraints | Constraints contradict or omit DPRD constraints | Constraints edited after placement | Return to PIK; restore from frozen DPRD |
| readiness | Readiness items unchecked | DPRD handed off before meeting readiness criteria | Return to PIK; complete outstanding readiness items before re-initiating handoff |

**Path B (generated from Product Brief):** Standard validator failure — generate a corrected PRD in a new session using the prompt and brief.

---

## Architecture Context File (ACF-{PROJECT}-{NNN})

| Gate | What Failure Looks Like | Typical Cause | Remediation |
|------|------------------------|---------------|-------------|
| document_control | Missing ID, owner, status, or scope | Template fields not filled | Complete §1 document control fields; all fields required |
| purpose | Purpose describes a specific design choice | ACF written as an architecture document rather than a guardrail document | Reframe: the ACF defines constraints and guardrails, not solutions or designs |
| platform_assumptions | Runtime, deployment, networking, or identity not stated | Incomplete scope coverage | Document each platform category explicitly; "Not applicable" with justification is acceptable |
| security_guardrails | Security guardrails stated aspirationally ("should be secure") | Security requirements written as goals rather than constraints | Write testable constraints: what is forbidden, what is required, with specific standards referenced |
| compliance | Compliance section blank with no "None applicable" justification | Section skipped | List applicable standards or state "None applicable — [specific reason]" |
| reliability | Availability or rollback expectations absent | Non-functional requirements omitted | State a minimum availability target and rollback expectation as verifiable, testable criteria |
| observability | Required telemetry types not defined | Observability left to implementation judgment | Define what must be logged, what metrics must be emitted, and the minimum required signal set |
| forbidden_patterns | No enforceable forbidden patterns listed | Patterns omitted as obvious or already known | List at least one concrete, enforceable forbidden pattern per concern area |
| constraint_enforceability | Aspirational language present ("try to", "as needed") | Standards written for guidance rather than governance | Replace aspirational language with absolute, testable terms: "MUST", "MUST NOT", specific numeric thresholds |

---

## System Architecture Design (SAD-{PROJECT}-{NNN})

| Gate | What Failure Looks Like | Typical Cause | Remediation |
|------|------------------------|---------------|-------------|
| intent_integrity | Intent Summary contradicts or expands PRD §1 | Drift from PRD during generation | Align §1 directly with PRD §1; quote or close-paraphrase the PRD language |
| scope_discipline | SAD introduces components or capabilities not in PRD scope | Architecture expands scope during design | Remove out-of-scope components; document them as deferred decisions in §9 |
| upstream_traceability | Architecture decisions not traceable to PRD requirements | Traceability missing or added as afterthought | Add requirement citations to each architecture decision (e.g., "PRD §3 R-001") |
| structural_diagrams | Required diagrams absent or incomplete | Diagrams omitted as optional | Include all required diagram types; at minimum: a component diagram and a data flow diagram |
| cross_cutting_concerns | Security, auth, or observability approach absent | Cross-cutting concerns left for TDD to resolve | Document the cross-cutting approach in SAD; the TDD implements it, not defines it |
| data_integration | Data model or integration patterns absent | Data concerns deferred | Address data storage, access patterns, and external integrations explicitly in the SAD |
| failure_modes | Known failure modes not documented | Failure analysis skipped | Identify at least 3 failure modes and describe their isolation and recovery approach |
| quality_attributes | Non-functional requirements not addressed | Quality attributes deferred to later stages | State how each SAD decision achieves the quality attributes established in PRD constraints |
| deferred_decisions | Decisions deferred without documentation | Deferrals invisible within the document | List each deferred decision with explicit justification in §9 |
| risk_awareness | Architectural risks not identified | Risk section absent | Identify the technical risks introduced by architectural choices and their proposed mitigations |
| guardrail_alignment | SAD choices contradict ACF guardrails | ACF not consulted during generation | Cross-check every SAD decision against ACF §3–§7 before finalizing |
| implementation_leakage | SAD specifies implementation details (specific frameworks, libraries, versions) | Architecture and implementation boundary blurred | Move implementation specifics to the TDD; SAD defines structure, not technology |

---

## Design Context File (DCF-{PROJECT}-{NNN})

| Gate | What Failure Looks Like | Typical Cause | Remediation |
|------|------------------------|---------------|-------------|
| document_control | Missing required fields | Template not completed | Fill §1 fields: ID, owner, status, scope |
| purpose | Purpose describes specific designs | DCF written as a design document | Reframe: the DCF defines design standards and expectations, not specific designs |
| design_principles | Principles aspirational (e.g., "write clean code") | Principles written as values rather than rules | Write as verifiable rules: define what constitutes a violation and what constitutes compliance |
| quality_bars | Quality bars unmeasurable (e.g., "high code quality") | Bars stated vaguely | Replace with metrics: coverage threshold percentage, response time target in ms, error rate limit |
| non_goals_enforcement | Non-goals section empty | Non-goals treated as optional | Define at least one concrete non-goal that actively prevents scope expansion |
| operational_expectations | Deployment or monitoring expectations absent | Operational context omitted | State: deployment mechanism, monitoring requirements, and runbook expectations |
| testing_expectations | Test layers or evidence requirements absent | Testing left to developer judgment | Define: required test layers, minimum coverage threshold, and what evidence is required for promotion |
| documentation_expectations | Required TDD sections not listed | Documentation requirements left to developer judgment | Specify which TDD sections, diagrams, and traceability markers are required |
| standard_enforceability | Aspirational language present | Standards written for guidance rather than enforcement | Replace with absolute, testable language throughout |

---

## Technical Design Document (TDD-{PROJECT}-{NNN})

| Gate | What Failure Looks Like | Typical Cause | Remediation |
|------|------------------------|---------------|-------------|
| intent_alignment | TDD scope exceeds or contradicts SAD scope | Scope drift from SAD during detailed design | Align §1 intent with SAD §1; remove all out-of-scope components |
| scope | Components listed without technology specification | Technology choices deferred to implementation | Specify the language, framework, and version for every component |
| interfaces | APIs or contracts described vaguely or undefined | Interfaces left abstract | Define each interface with method signature, input/output schema, and error contract |
| build_deploy | Build or deployment mechanism not described | Build and deploy steps deferred | Document build steps, artifact type, and deployment mechanism explicitly |
| failure_handling | Failure handling absent or marked "TBD" | Failure modes deferred | Define failure handling for each component's known failure modes, as established in SAD |
| testing | Test plan absent or describes only unit tests | Testing scope too narrow | Document test layers required, what each layer validates, and coverage targets |
| readiness | Readiness checklist incomplete | Checklist not reviewed before freeze | Address each checklist item; provide written justification for any item intentionally skipped |

---

## Work Design Document (WDD-{PROJECT}-{NNN})

| Gate | What Failure Looks Like | Typical Cause | Remediation |
|------|------------------------|---------------|-------------|
| traceability | WDD items not traceable to TDD components or requirements | Work items invented during decomposition | Each WDD item must cite the TDD component or requirement it implements |
| scope | WDD items collectively exceed TDD scope | Scope additions introduced during decomposition | Remove items not traceable to TDD; document them in a future engagement |
| atomicity | Work items bundled (multiple concerns in one item) | Decomposition stopped before reaching atomic units | Split each item until each concern can be independently tested and reviewed |
| inputs_outputs | Input/output contracts not defined per work item | Interfaces deferred to implementation | Define for each work item: what it receives, what it produces, and the contract it must satisfy |
| acceptance_criteria | Acceptance criteria absent or binary ("it works") | Criteria not written to be testable | Write observable, verifiable conditions that describe what the reviewer will check |
| granularity | Items too large (estimated >1 day) or too small (<30 min meaningful unit) | Decomposition not calibrated to appropriate granularity | Re-decompose to appropriate granularity as defined in DCF guidance |
| readiness | Definition of Ready not met | DoR check skipped before freeze | Run the DoR validator before freezing; address every failing criterion |
| work_groups | Work groups not sequenced | Dependencies not mapped | Define work groups with explicit dependency order; distinguish parallel from sequential groups |

---

## Operational Readiness Document (ORD-{PROJECT}-{NNN})

| Gate | What Failure Looks Like | Typical Cause | Remediation |
|------|------------------------|---------------|-------------|
| deployment_verification | Deployment steps are assertion-only or unverified | Deployment not verified before documentation | Replace assertions with concrete evidence: log reference, screenshot, or test output |
| observability_verification | Monitoring not confirmed live | Observability assumed to be working | Show a live query result, dashboard screenshot, or monitoring alert as evidence |
| alerting_verification | Alerting not confirmed | Alerting assumed from configuration | Confirm each alert fires correctly with test evidence |
| failure_handling_verification | Failure scenarios not tested | Failure handling assumed from TDD documentation | Document actual failure test results; "not tested" is a blocking issue |
| security_verification | Security checks not performed | Security assumed from ACF guardrails | Document evidence of security review: scan results, penetration test output, or code review findings |
| runbook_verification | Runbook absent or incomplete | Runbook deferred past release readiness | Document runbook location and confirm it covers all major failure scenarios |
| no_open_blockers | Open Items table has unresolved blockers | Issues carried forward without resolution | Resolve or explicitly accept (with justification and named owner) every open item |
| evidence_quality | Evidence is assertion-based ("it works", "confirmed") | Evidence not gathered from actual verification | Replace assertions with retrievable evidence: commit SHA, CI run ID, log timestamp |
