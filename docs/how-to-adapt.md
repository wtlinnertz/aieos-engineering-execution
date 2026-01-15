# How to Adapt ai-sdlc-kit to Your Organization

This guide explains **what you should customize**, **what must remain invariant**, and **how to layer organizational policy** on top of ai-sdlc-kit without breaking its guarantees.

The goal is adoption without dilution.

---

## First: What This Kit Is (and Is Not)

ai-sdlc-kit is:
- A **process and artifact model**
- A **set of enforceable quality gates**
- A **safe way to use AI across the SDLC**

ai-sdlc-kit is not:
- A toolchain
- A framework
- A prescriptive architecture
- A replacement for engineering judgment

You are expected to adapt it — **carefully**.

---

## Invariants (Do Not Change These)

The following concepts must remain intact if you want the system to work as designed.

### 1. Artifact Promotion Model
- Artifacts are **promoted**, not rewritten
- Each artifact has a **single responsibility**
- Downstream artifacts may not expand scope

If this breaks, rework and ambiguity return immediately.

---

### 2. Freeze Points
- PRD, SAD, TDD, and WDD must have explicit freeze moments
- Promotion without freeze undermines traceability

You may automate freezes, but you may not remove them.

---

### 3. Validators as Hard Gates
- Validators decide readiness
- Ambiguity is a failure condition
- Validators do not redesign or suggest solutions

Soft validators turn this into documentation theater.

---

### 4. Non-Goals Are Enforceable
- Non-goals must be explicit
- Validators must block violations
- “Helpful” scope expansion is not allowed

This is the most common failure mode in adoption.

---

### 5. Granularity Rules for Execution
- One outcome
- One repo/subsystem
- One PR
- One environment

If you relax granularity, AI effectiveness drops sharply.

---

## What You SHOULD Customize

These are the **intended extension points**.

---

### 1. Architecture Context Files (ACFs)

ACFs are the primary place to encode:
- Security requirements
- Compliance constraints
- Platform standards
- Forbidden patterns
- Approved integrations

You will almost certainly need:
- One global ACF
- Optional domain- or platform-specific ACFs

ACFs let you adapt without changing templates or validators.

---

### 2. Design Context Files (DCFs)

DCFs define:
- Design principles
- Quality bars
- Testing expectations
- Operational standards

Use DCFs to:
- Encode engineering standards
- Set quality expectations
- Align multiple teams

Do not hardcode these into templates.

---

### 3. Validator Integration Points

You may:
- Integrate validators into CI
- Use them in internal portals
- Enforce them via workflow transitions
- Run them manually during reviews

You may also:
- Add **organization-specific gates**
- Add **additional validators**

Do not weaken existing gates without understanding the trade-offs.

---

### 4. Jira (or Work System) Field Mapping

You will need to adapt:
- Field names
- Labels
- Custom fields
- Workflow states

This does **not** require changing:
- Story structure
- DoR logic
- Granularity rules

Map the model to your tooling, not the other way around.

---

### 5. Enforcement Strictness (Carefully)

You may choose:
- Where enforcement is automated
- Where humans approve
- Where exceptions are allowed

If you allow exceptions:
- Make them explicit
- Make them visible
- Make them rare

Invisible exceptions rot the system.

---

## What You Should Avoid Customizing Early

These changes usually cause more harm than good.

### ❌ Combining Artifacts
Example: “Let’s merge SAD and TDD.”

This removes clarity and makes validation harder.

---

### ❌ Making Validators Helpful
Example: “Let’s have the validator suggest improvements.”

This causes silent redesign and scope creep.

---

### ❌ Overloading Templates
Example: Adding org-specific fields everywhere.

Use ACFs and DCFs instead.

---

### ❌ Skipping Intent Summary Pre-Pass
This is the cheapest alignment step in the system.

Skipping it costs more later.

---

## Recommended Adoption Path

A proven, low-risk path:

1. Use the kit **as-is** on one pilot initiative
2. Add a **minimal ACF**
3. Add a **minimal DCF**
4. Integrate the **DoR validator** first
5. Expand enforcement gradually
6. Only then customize further

Do not start by changing templates or validators.

---

## Scaling Across Teams

When scaling:
- Centralize ACF ownership
- Version ACFs and DCFs
- Treat changes as breaking or non-breaking
- Communicate changes explicitly

The model scales best when **context is centralized** and **execution is decentralized**.

---

## Common Failure Modes

Watch for:
- “Just this once” scope expansion
- Validators being bypassed silently
- WDD items growing beyond atomic size
- Non-goals being treated as suggestions
- Teams copying examples without adapting context

These are signals to tighten enforcement, not loosen it.

---

## Final Guidance

ai-sdlc-kit is intentionally opinionated where it matters.

Adapt the **edges**, not the **core**.

If you preserve:
- Clear intent
- Explicit scope
- Hard gates
- Atomic execution

Then AI-assisted delivery becomes predictable, safe, and scalable.

