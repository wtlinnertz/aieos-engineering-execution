# Session Setup — Engineering Execution Kit

Use this file to set up an AI session for each EEK artifact. Find the section for the artifact you are generating or validating. Follow the checklist before starting.

**Rule:** Generate and validate in separate sessions. Do not self-validate.

---

## Kit Entry Record (KER)

**What you're creating:** The formal gate that authorizes EEK work to begin, recording the entry path and upstream artifact references.

**Note:** The KER is human-authored. Complete it directly using the template — no generation prompt.

**Required Inputs (confirm before starting):**
- [ ] Frozen WCR — Frozen and routing to EEK?
- [ ] Path A: Frozen DPRD (DPRD-{PROJECT}-{NNN}) — Frozen and delivered?
- [ ] Path B: Product Brief (human input) — Complete and bounded?

**Pre-Flight Gate Check (verify before generating):**
- [ ] `document_control`: ID, date, owner, and version fields ready
- [ ] `classification_check`: WCR routing matches the path you are selecting
- [ ] `path_selected`: Path A or Path B explicitly selected with upstream artifact ID recorded
- [ ] `priority_on_record`: Priority classification and its source are known
- [ ] `scope_bounded`: KER scope matches upstream artifact scope exactly

**Session Setup:**
1. Use: `docs/artifacts/kit-entry-template.md` — fill manually, no prompt
2. Validate: Not governed by a validator — human review and freeze
3. After KER is frozen, proceed to PRD step

**Common Failure to Avoid:**
Selecting Path A without recording the DPRD ID — the KER must explicitly cite the frozen DPRD artifact ID, confirming the handoff is complete.

---

## PRD

**What you're creating:**
- **Path A:** Placing (not regenerating) the DPRD from PIK as `docs/sdlc/01-prd.md` and running an acceptance check
- **Path B:** Generating a PRD from a Product Brief using the PRD prompt

**Path A — DPRD Acceptance (no generation):**

**Required Inputs (confirm before starting):**
- [ ] Frozen DPRD — Delivered from PIK?
- [ ] Frozen KER — Frozen with Path A selected?

**Pre-Flight Gate Check:**
- [ ] DPRD has not been modified since receipt from PIK
- [ ] DPRD consistency check completed: `docs/prompts/dprd-consistency-check-prompt.md`

**Session Setup (Path A):**
1. Place the frozen DPRD at `docs/sdlc/01-prd.md` without modification
2. Run consistency check in a session using `docs/prompts/dprd-consistency-check-prompt.md`
3. Validate in a separate session: `docs/validators/prd-validator.md` (acceptance check)
4. If validation FAILS: return to PIK — do not modify the placed DPRD

**Path B — PRD Generation:**

**Required Inputs:**
- [ ] Completed Product Brief — Present and bounded?
- [ ] Frozen KER — Frozen with Path B selected and justification present?

**Pre-Flight Gate Check:**
- [ ] `problem_definition`: Product Brief has a clear problem statement
- [ ] `goals`: Success criteria are measurable
- [ ] `scope`: Scope is bounded and non-goals are identified
- [ ] `requirements`: Outcome requirements (not implementation) are ready to surface
- [ ] `constraints`: Organizational constraints are known

**Session Setup (Path B):**
1. Load: `docs/prompts/prd-prompt.md`
2. Provide: Complete Product Brief and frozen KER
3. Provide: `docs/specs/prd-spec.md` (or confirm it is in context)
4. Run consistency check: `docs/prompts/dprd-consistency-check-prompt.md` (Path B check)
5. Validate in a separate session: `docs/validators/prd-validator.md`

**Common Failure to Avoid:**
Regenerating the DPRD on Path A — the DPRD is placed as-is. Any errors found during acceptance check return to PIK for correction.

---

## Architecture Context File (ACF-{PROJECT}-{NNN})

**What you're creating:** Organizational architecture guardrails that all SAD decisions must satisfy — not the architecture itself.

**Required Inputs (confirm before starting):**
- [ ] Frozen PRD — Frozen?
- [ ] Architecture Context Form (intake form) — Completed?
- [ ] Engineering principles docs — Available in `docs/principles/`?

**Pre-Flight Gate Check (verify before generating):**
- [ ] `document_control`: ID, owner, status, and scope planned
- [ ] `purpose`: Will describe guardrails, not specific architecture choices
- [ ] `platform_assumptions`: Runtime, deployment, networking, identity are known
- [ ] `security_guardrails`: At least one testable security constraint identified
- [ ] `compliance`: Applicable compliance standards (or "None applicable") are known
- [ ] `reliability`: Availability target and rollback expectation are defined
- [ ] `observability`: Required telemetry types are known
- [ ] `forbidden_patterns`: At least one enforceable forbidden pattern identified
- [ ] `constraint_enforceability`: All constraints will be testable, not aspirational

**Session Setup:**
1. Load: `docs/prompts/acf-prompt.md`
2. Provide: Completed Architecture Context Form
3. Provide: Relevant engineering principles from `docs/principles/`
4. Provide: `docs/specs/acf-spec.md` (or confirm it is in context)
5. Validate in a separate session: `docs/validators/acf-validator.md`

**Common Failure to Avoid:**
Writing guardrails aspirationally ("should be secure", "try to use caching") — use absolute, testable language: "MUST", "MUST NOT", specific metrics and thresholds.

---

## System Architecture Design (SAD-{PROJECT}-{NNN})

**What you're creating:** The structural architecture of the system — components, interactions, data flows, and failure modes — bounded by PRD scope and ACF guardrails.

**Required Inputs (confirm before starting):**
- [ ] Frozen PRD — Frozen?
- [ ] Frozen ACF — Frozen?
- [ ] Any existing Architecture Decision Records (ADRs) — Available?

**Pre-Flight Gate Check (verify before generating):**
- [ ] `intent_integrity`: SAD intent will align with PRD §1 exactly (no expansion)
- [ ] `scope_discipline`: Only PRD-scoped components will be designed
- [ ] `upstream_traceability`: Each decision will cite its PRD requirement source
- [ ] `structural_diagrams`: Will include component diagram + data flow at minimum
- [ ] `cross_cutting_concerns`: Security, auth, and observability approach will be addressed
- [ ] `failure_modes`: At least 3 known failure modes will be documented
- [ ] `guardrail_alignment`: Every choice will be cross-checked against ACF §3–§7
- [ ] `implementation_leakage`: No framework/library specifics — those belong in TDD

**Session Setup:**
1. Load: `docs/prompts/sad-prompt.md`
2. Provide: Full frozen PRD and frozen ACF
3. Provide: Any existing ADRs relevant to this initiative
4. Provide: `docs/specs/sad-spec.md` (or confirm it is in context)
5. Validate in a separate session: `docs/validators/sad-validator.md`

**Common Failure to Avoid:**
SAD intent summary that contradicts or expands PRD scope — the intent summary must align with PRD §1; quote or close-paraphrase it.

---

## Design Context File (DCF-{PROJECT}-{NNN})

**What you're creating:** Organizational design standards that all TDD decisions must satisfy — code quality, testing, documentation, and operational expectations.

**Required Inputs (confirm before starting):**
- [ ] Frozen PRD — Frozen?
- [ ] Frozen ACF — Frozen?
- [ ] Design Context Form (intake form) — Completed?

**Pre-Flight Gate Check (verify before generating):**
- [ ] `document_control`: ID, owner, status, scope planned
- [ ] `design_principles`: Will write actionable (not aspirational) principles
- [ ] `quality_bars`: At least one measurable quality bar identified (coverage %, response time)
- [ ] `non_goals_enforcement`: At least one concrete non-goal ready
- [ ] `operational_expectations`: Deployment, monitoring, and auditability expectations known
- [ ] `testing_expectations`: Required test layers, evidence requirements, promotion gates known
- [ ] `documentation_expectations`: Required TDD sections, diagrams, traceability markers known
- [ ] `standard_enforceability`: All standards will be testable, not aspirational

**Session Setup:**
1. Load: `docs/prompts/dcf-prompt.md`
2. Provide: Completed Design Context Form
3. Provide: Frozen ACF (design standards must not conflict with architecture guardrails)
4. Provide: `docs/specs/dcf-spec.md` (or confirm it is in context)
5. Validate in a separate session: `docs/validators/dcf-validator.md`

**Common Failure to Avoid:**
Quality bars stated vaguely ("high code quality") — replace with specific, measurable criteria: test coverage threshold, maximum acceptable response time, error rate limit.

---

## Technical Design Document (TDD-{PROJECT}-{NNN})

**What you're creating:** Component-level technical design — interfaces, technology choices, build/deploy, testing plan — implementing the SAD architecture within DCF constraints.

**Required Inputs (confirm before starting):**
- [ ] Frozen SAD — Frozen?
- [ ] Frozen DCF — Frozen?
- [ ] Frozen ACF — Frozen? (for guardrail cross-check)

**Pre-Flight Gate Check (verify before generating):**
- [ ] `intent_alignment`: Will align §1 with SAD §1 — no expansion
- [ ] `scope`: Every component will have a language/framework specification
- [ ] `interfaces`: API contracts, input/output schema, error contracts will be defined
- [ ] `build_deploy`: Build steps and deployment mechanism will be documented
- [ ] `failure_handling`: Failure handling for each SAD failure mode will be addressed
- [ ] `testing`: Test layers, what each validates, and coverage targets will be defined
- [ ] `readiness`: All readiness checklist items are satisfiable

**Session Setup:**
1. Load: `docs/prompts/tdd-prompt.md`
2. Provide: Full frozen SAD and frozen DCF
3. Provide: Frozen ACF (guardrail verification)
4. Provide: `docs/specs/tdd-spec.md` (or confirm it is in context)
5. Validate in a separate session: `docs/validators/tdd-validator.md`

**Common Failure to Avoid:**
Components listed without technology specification — every component must name language, framework, and version. "TBD" fails the scope gate.

---

## Work Design Document (WDD-{PROJECT}-{NNN})

**What you're creating:** Decomposed, atomic work items with traceability, acceptance criteria, and dependency sequencing — ready for execution.

**Required Inputs (confirm before starting):**
- [ ] Frozen TDD — Frozen?
- [ ] Frozen DCF — Frozen? (for granularity guidance)

**Pre-Flight Gate Check (verify before generating):**
- [ ] `traceability`: Each work item will cite a TDD component or requirement
- [ ] `scope`: WDD scope will not exceed TDD scope
- [ ] `atomicity`: Work items will be independently testable and reviewable
- [ ] `inputs_outputs`: Each item will have a defined contract (input, output, interface)
- [ ] `acceptance_criteria`: Criteria will be observable and verifiable, not binary
- [ ] `granularity`: Items will be calibrated to ~1 day or less per DCF guidance
- [ ] `readiness`: Definition of Ready criteria are known and will be met
- [ ] `work_groups`: Dependency order between work groups will be defined

**Session Setup:**
1. Load: `docs/prompts/wdd-prompt.md`
2. Provide: Full frozen TDD
3. Provide: Frozen DCF (for granularity and testing expectations)
4. Provide: `docs/specs/wdd-spec.md` (or confirm it is in context)
5. After generation: run DoR validator, Consistency Validator, then WDD Validator — in separate sessions
6. Validate in a separate session: `docs/validators/wdd-validator.md`

**Common Failure to Avoid:**
Bundled work items (multiple concerns in one item) — decompose until each item can be independently tested, reviewed, and merged.

---

## Operational Readiness Document (ORD-{PROJECT}-{NNN})

**What you're creating:** A verification record confirming the implementation is observable, deployable, and safe to hand off to REK — with concrete evidence, not assertions.

**Required Inputs (confirm before starting):**
- [ ] Frozen WDD — Frozen?
- [ ] Frozen TDD — Frozen?
- [ ] Frozen ACF — Frozen?
- [ ] Frozen DCF — Frozen?
- [ ] Actual implementation evidence — Available? (deployment logs, test results, monitoring screenshots)

**Pre-Flight Gate Check (verify before generating):**
- [ ] `deployment_verification`: Actual deployment evidence in hand (not assumed)
- [ ] `observability_verification`: Live monitoring query or dashboard confirmed
- [ ] `alerting_verification`: Alerts confirmed firing correctly
- [ ] `failure_handling_verification`: At least one failure scenario actually tested
- [ ] `security_verification`: Security evidence gathered (scan results, review findings)
- [ ] `runbook_verification`: Runbook exists and covers major failure scenarios
- [ ] `no_open_blockers`: All open items resolved or explicitly accepted with justification
- [ ] `evidence_quality`: All evidence is retrievable (log timestamp, CI run ID, commit SHA)

**Session Setup:**
1. Load: `docs/prompts/ord-prompt.md`
2. Provide: Frozen WDD, TDD, ACF, DCF
3. Provide: All implementation evidence (deployments, tests, monitoring, security)
4. Provide: `docs/specs/ord-spec.md` (or confirm it is in context)
5. Validate in a separate session: `docs/validators/ord-validator.md`
6. After PASS: hand off frozen ORD to Release & Exposure Kit

**Common Failure to Avoid:**
Evidence-by-assertion ("deployment succeeded") without retrievable proof — replace with a log reference, CI run ID, or screenshot. Assertions fail the evidence_quality gate.
