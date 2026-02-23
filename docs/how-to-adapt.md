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

### 4. Work System Field Mapping

You will need to adapt:
- Field names
- Labels
- Custom fields
- Workflow states

This does **not** require changing:
- WDD work item structure
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

### ❌ Removing Intent Verification from Prompts
Intent verification is the cheapest alignment step in the system.

Removing it from prompts costs more later — validators catch misalignment, but only after a full generation cycle.

---

## Setting Up Your Project

### Where the Kit Lives

Keep the kit as a **central, shared repository**. Teams reference the kit's prompts, validators, and templates — they do not copy them into each project. When the kit improves, every team benefits immediately.

### Where Artifacts Live

Generated artifacts (PRD, SAD, TDD, WDD, ORD) live in the **app repo** under a conventional path:

```
my-app/
  docs/sdlc/
    01-prd.md
    02-sad.md
    03-tdd.md
    04-wdd.md
    05-ord.md
```

The numbered prefix follows the artifact flow, so the directory listing reads in promotion order.

**Why the app repo?** Artifacts describe *this* system — they should live with it. Git gives you version history, branching, and freeze semantics for free. PRD changes and code changes share the same history, making traceability natural.

### Where ACF and DCF Live

This depends on your organization:

**If your organization has shared standards** — ACF and DCF live in a central location (the kit repo, a governance repo, or wherever org-level standards are maintained). Projects reference them; they are not duplicated per project.

**If teams set their own standards** — ACF and DCF are project-level artifacts and live in `docs/sdlc/` alongside everything else:

```
my-app/
  docs/sdlc/
    01-prd.md
    02-acf.md
    03-sad.md
    04-dcf.md
    05-tdd.md
    06-wdd.md
    07-ord.md
```

**If some standards are shared and some are team-specific** — Use an org-level ACF/DCF for the shared baseline. Teams can extend with a project-level ACF/DCF that adds team-specific constraints without contradicting the org-level file. Reference both as inputs when generating artifacts.

Start with project-level context files. Extract to a shared location only when you've proven reuse across multiple projects.

### Where Human-Authored Inputs Live

Some artifacts require human-authored inputs before they can be generated:

- **Product Brief** — feeds the PRD prompt (template: `product-brief-template.md`)
- **Architecture Context** — feeds the ACF prompt (template: `architecture-context-template.md`)
- **Design Context** — feeds the DCF prompt (template: `design-context-template.md`)
- **System Context** — feeds the SAD prompt for brownfield projects (template: `system-context-template.md`)

These are not AI-generated artifacts — they are the human decisions that drive generation. For brownfield projects, the `codebase-analysis-prompt.md` can pre-fill the Architecture Context, Design Context, and System Context forms from codebase analysis — the human reviews and edits before using them as input.

Store intake forms in `docs/sdlc/` with a `00-` prefix to distinguish them from generated artifacts:

```
my-app/
  docs/sdlc/
    00-product-brief.md          ← human input
    00-architecture-context.md   ← human input
    00-design-context.md         ← human input (optional, for DCF)
    00-system-context.md         ← human input (brownfield only, for SAD)
    01-prd.md                    ← generated, validated, frozen
    02-acf.md
    ...
```

The `00-` prefix keeps inputs visually grouped before the generated artifacts in directory listings.

### Configuring AI Tool Awareness

Most AI tools support project-level instruction files that are loaded at the start of every session. Use these to give the AI automatic awareness of the kit and your project's conventions — without manually pasting instructions each time.

See `how-to-use-with-ai.md` → "Setting Up Your AI Tool" for the specific file names per tool and what to include.

At minimum, the instruction file should reference:
- The kit's location (path to prompts, specs, templates, validators)
- Your project's artifact directory (`docs/sdlc/`)
- The artifact flow and key constraints (freeze-before-promote, separate sessions)

This is especially important when the kit is a shared repository separate from the project repo — the AI needs to know where to find both.

---

### Where Validator Results Live

Store the final passing validator result alongside the artifact it validated:

```
my-app/
  docs/sdlc/
    01-prd.md
    01-prd-validation.json
    02-acf.md
    02-acf-validation.json
    ...
```

The pattern is `{nn}-{type}-validation.json`. Store only the final passing result — the one that gates the freeze decision. Earlier failed attempts are in git history if needed.

---

## Recommended Adoption Path

A proven, low-risk path:

1. Use the kit **as-is** on one pilot initiative
2. Set up `docs/sdlc/` in your app repo
3. Add a **minimal ACF** (project-level is fine to start)
4. Add a **minimal DCF** (project-level is fine to start)
5. Integrate the **DoR validator** first
6. Expand enforcement gradually
7. Extract ACF/DCF to a shared location only after reuse is proven
8. Only then customize further

Do not start by changing templates or validators.

---

## Scaling Across Teams

When scaling:
- Extract proven ACFs and DCFs to a shared location
- Assign ownership for shared context files
- Version ACFs and DCFs — treat changes as breaking or non-breaking
- Communicate changes explicitly
- Allow team-level extensions that add constraints without contradicting shared standards

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

