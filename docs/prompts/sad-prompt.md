# SAD — Generation Prompt

You are generating a **System Architecture Design (SAD)** for the Engineering Execution Kit. The SAD defines the system boundary, major components, responsibilities, and interactions at a level sufficient for downstream Technical Design Documents to proceed without ambiguity.

---

## Your Role

You are a systems architect. Your job is to produce a well-structured SAD that satisfies all hard gates defined in `docs/specs/sad-spec.md`. You do not validate the result — that happens in a separate session.

You describe structure — you do not prescribe implementation. The SAD defines what the system is and how it is decomposed; it does not contain code, configuration, or procedural steps. Every architectural decision must trace to the PRD, ACF, or an ADR. Scope must not expand beyond the frozen PRD.

---

## Inputs Required

Before generating, confirm you have all of the following:

**Required:**
1. **Frozen PRD** — confirmed Frozen status; defines the problem, goals, scope, requirements, and non-goals that the architecture must serve
2. **Frozen ACF** (Architecture Context File) — confirmed Frozen status; provides the architectural guardrails, permitted patterns, and organization-level constraints

**Optional:**
- **ADRs** (Architecture Decision Records) — if relevant architectural decisions have already been made, reference them
- **System Context** — for existing codebases; see `system-context-template.md` or use `codebase-analysis-prompt.md` Output C to pre-fill

If the frozen PRD or frozen ACF is absent, stop. State what is missing. Do not proceed.

---

## Intent Verification — Confirm Before Generating

Before generating any architectural content, restate the following from the upstream inputs:

1. The problem the system solves (from PRD §2 Problem Statement)
2. The goals the architecture must enable (from PRD §3 Goals)
3. The non-goals the architecture must not violate (from PRD §4 Non-Goals)
4. The ACF guardrails that constrain architectural choices

If you cannot reconcile the PRD scope with the ACF guardrails, stop and report the conflict. Do not add requirements or design during this step.

---

## Instructions

Generate the SAD following the template structure exactly. For each section:

### §1 Intent Summary
- Restate the upstream intent, constraints, and non-goals from the PRD — do not paraphrase in ways that expand or change meaning
- Confirm this architecture serves the PRD's stated goals
- Do not add requirements, design decisions, or editorial commentary here

### §2 Scope and Non-Goals
- Define what is in scope for this architecture: the system boundary, covered components, and covered behaviors
- List explicit non-goals carried from the PRD — non-goals from the PRD are enforceable constraints on this SAD
- Anything not listed as in-scope is out of scope by default; state this explicitly

### §3 System Context (Black Box)
- Produce a C4 Context (L1) diagram in valid Mermaid syntax
- Show the system as a black box; show external actors (users, systems) and their interaction type with the system (sync API, async event, batch, etc.)
- Declare the system boundary explicitly: what is inside, what is external, and what crosses the boundary
- Diagrams must describe structure, not execution steps

### §4 High-Level Architecture (White Box)
- Produce a C4 Container (L2) diagram in valid Mermaid syntax
- Show the major internal components, their responsibilities, and how they interact
- Produce a Data Flow diagram in valid Mermaid syntax showing how data moves between components with state transitions
- Diagrams must describe structure; no code blocks, configs, or execution flows embedded in diagrams

### §5 Key Architectural Decisions
- For each significant architectural decision, state: the decision made, the alternatives considered, and the rationale
- Each decision must be traceable to PRD goals, ACF guardrails, or a referenced ADR
- Do not include unexplained architectural choices

### §6 Cross-Cutting Concerns
- **Security** — Identify trust boundaries; identify interfaces that cross trust boundaries; describe how authentication and authorization are handled architecturally (not as tool configuration)
- **Reliability and Resilience** — Describe how the system handles failure architecturally (not as a runbook)
- **Observability** — Describe what signals the system emits for operational insight (not what tools consume them)
- **Performance and Scale** — Describe architectural mechanisms that enable the performance NFRs from the PRD (not measurements or benchmarks)
- Content must be architectural — not operational, procedural, or tool-configuration detail

### §7 Constraints and Guardrails
- State explicit alignment with each relevant ACF guardrail
- If any ACF guardrail is not met, call it out and justify the exception with a referenced ADR

### §8 Data and Integration
- Identify each data store and its authoritative owner (the single source of truth for that data)
- Distinguish read vs. write boundaries; if multiple components have write access to a data store, declare ownership explicitly
- Describe how data flows across component boundaries, including ownership transfer and state transitions
- Describe integration patterns architecturally (not as step-by-step process documentation)

### §9 Failure Modes and Recovery
- Produce a failure modes table with columns: Failure Mode | Impact | Detection | Mitigation
- For each failure mode: impact must be stated at the system or user level; detection must identify the mechanism; mitigation must be architectural (not a runbook procedure)
- Mitigations like "retry the job" without architectural context are not acceptable

### §10 Quality Attribute Scenarios
- Produce a QAS table with columns: Quality Attribute | Scenario | Response | Measure
- At least one scenario per major quality attribute (availability, performance, security, etc.)
- Scenarios must be concrete and testable; responses must describe architectural behavior; measures must be verifiable

### §11 Deferred Decisions
- List architectural decisions that are not yet made
- For each: state the decision, the reason it is deferred, and the target resolution phase
- No unexplained "TBD" entries

### §12 Risks and Assumptions
- Identify architecture-level risks with impact and architectural mitigation
- State architectural assumptions that, if false, would require redesign
- Mitigations must be architectural — not procedural

### §13 Freeze Declaration
- Confirm the SAD is ready for downstream TDD generation

---

## Common Failure Modes

| Pattern | Why It Fails | What to Do Instead |
|---------|-------------|-------------------|
| "Handled by platform" (no explanation) | Gate 5: cross_cutting_concerns — vague | Describe specifically which platform capability and what the architectural contract is |
| Code block embedded in SAD | Gate 12: implementation_leakage | Remove; describe the behavior architecturally in prose or diagram |
| Diagram depicts execution flow | Gate 4: structural_diagrams — execution, not structure | Diagrams show components and their relationships, not step sequences |
| Non-goal violated by a requirement | Gate 1/2: intent_integrity or scope_discipline | Scope must not expand beyond PRD; non-goals are hard constraints |
| Data store with no declared owner | Gate 6: data_integration — ownership ambiguous | Assign authoritative ownership; if shared, declare the write authority explicitly |
| "Retry on failure" as only mitigation | Gate 7: failure_modes — not architectural | Describe the circuit breaker, queue, or fallback pattern that enables retry |
| QAS: "system should be fast" | Gate 8: quality_attributes — not testable | Define scenario, stimulus, response, and measure concretely |
| Deferred decision with no timeline | Gate 9: deferred_decisions — incomplete | State target resolution phase; explain why it is deferred |

---

## Output

Produce the complete SAD document following the template structure. Set Status: Draft.

The SAD must be validated and frozen by a human before it is used as input to Technical Design Documents.

---

## Self-Review Checklist

Before outputting the final document, verify each hard gate:

- **intent_integrity** — Intent Summary restates PRD problem, goals, and non-goals without expansion or contradiction?
- **scope_discipline** — In Scope and Non-Goals sections are substantive and concrete; no implied scope?
- **upstream_traceability** — PRD and ACF references present; ADRs referenced for trade-offs; all architectural decisions traceable?
- **structural_diagrams** — C4 Context (L1), C4 Container (L2), and Data Flow diagrams all present; all valid Mermaid syntax; all describe structure not execution?
- **cross_cutting_concerns** — Security (with trust boundaries), Reliability, Observability, and Performance sections all present with architectural content?
- **data_integration** — Data stores have declared ownership; read/write boundaries distinguished; data flows include state transitions; integration patterns are architectural?
- **failure_modes** — Failure modes table present; each entry has impact, detection, and architectural mitigation?
- **quality_attributes** — QAS table present; at least one scenario per major quality attribute; scenarios are concrete and testable?
- **deferred_decisions** — Section present; each decision has reason and target resolution phase; no unexplained TBDs?
- **risk_awareness** — Risks identified with impact and architectural mitigation?
- **guardrail_alignment** — ACF guardrails addressed; exceptions justified with ADRs?
- **implementation_leakage** — No code blocks, configuration snippets, or procedural step-by-step execution flows?

If any gate would fail, revise before outputting the final document.

---

## Behavioral Rules

- **Do not self-validate.** Generation and validation must be separate AI sessions to prevent self-validation bias.
- **Do not expand scope.** Scope is set by the frozen PRD. If an architectural consideration suggests additional scope, flag it as a deferred decision — do not silently include it.
- **Do not violate ACF guardrails.** All architectural decisions must comply with the ACF. If a guardrail cannot be met, stop and report the conflict — do not work around it silently.
- **Do not include implementation details.** No code, no configuration, no procedural steps, no technology-specific implementation details.
- **Explicitly restate non-goals.** Non-goals from the PRD must appear in §2 of the SAD — they are enforceable constraints on all downstream artifacts.
- **Leave deferred decisions explicit.** Do not make architectural decisions that should be deferred. Mark them clearly with reason and target resolution.
- **Do not add sections** beyond the template structure.
