# Engineering Standards

## Purpose

This document defines the mandatory engineering standards governing:

* Architecture design
* Test design
* Implementation code
* AI-assisted development

These standards are enforceable across all repositories and apply to both human and AI-generated contributions.

The principles are informed by:

* Code Complete
* Clean Code
* The Pragmatic Programmer

The books are inspirational.
The rules below are enforceable.

---

# 1. Core Engineering Doctrine

## 1.1 Readability Over Cleverness

* Code must optimize for clarity, not brevity.
* Avoid dense expressions and clever shortcuts.
* Prefer explicitness over compression.
* A mid-level engineer must understand logic within 30 seconds.

If readability conflicts with brevity, readability wins.

---

## 1.2 Single Responsibility Principle (SRP)

### Functions

* Must do one thing.
* If a description contains “and”, it must be split.
* Soft limit: ≤ 30 lines.
* Hard limit: 50 lines unless justified.

### Classes / Modules

* Must represent one cohesive responsibility.
* No “manager”, “helper”, or “util” dumping grounds.
* Business logic must not mix with infrastructure logic.

---

## 1.3 Clear Naming

* Names must reveal intent.
* Avoid ambiguous abbreviations.
* No single-letter variables except loop counters.
* Boolean variables must read as predicates:

  * `isValid`
  * `hasPermission`
  * `shouldRetry`

Names must eliminate the need for explanatory comments.

---

## 1.4 Self-Documenting Code

Code must read like a narrative — a reader should understand *what happens and why* by reading top-to-bottom without jumping between files.

### Module Orientation

* A file's purpose must be clear from its first few lines.
* Group related logic together — do not scatter a single concern across a file.
* Public API at the top, private helpers below.
* A reader encountering the file for the first time should understand its role before reaching implementation details.

### Narrative Flow Within Functions

* Structure functions as: setup → action → result.
* Use guard clauses and early returns to clear away edge cases first, so the main logic reads as a clean, uninterrupted path.
* Avoid interleaving validation and business logic — validate upfront, then execute.
* Sequential steps should appear in execution order.

### Extraction Over Comments

* When a block of code needs a comment to explain *what* it does, extract it into a well-named function instead.
* The function name becomes the documentation.
* Prefer `calculateShippingDiscount(order)` over `// calculate the shipping discount` followed by inline math.
* Extract when naming improves comprehension, not when it merely reduces line count.

### When Comments Are Required

Comments are warranted only when code cannot speak for itself:

* **Why comments** — business rationale, regulatory constraints, non-obvious design choices.
* **Warning comments** — performance traps, ordering dependencies, "this looks wrong but is correct because...".
* **Contract comments** — public API boundaries where callers need usage guidance.

Comments that restate what code already says (e.g., `// increment counter` above `counter++`) are prohibited — they add noise and drift from the implementation.

### File and Symbol Ordering

* Organize files so a reader encounters concepts in dependency order: high-level first, details later.
* Constants and types at the top, public functions next, private helpers last.
* Within a class, group by behavior (all methods for one capability together), not by visibility modifier.

Code that requires a map to navigate is code that needs restructuring.

---

## 1.5 No Duplication (Strict DRY)

* Repeated logic must be extracted.
* Magic strings must be constants.
* Configuration must not be hardcoded.
* Cross-module duplication must be refactored.

Duplication compounds future risk.

---

## 1.6 Complexity Control

### Nesting

* Maximum recommended nesting depth: 3 levels.
* Prefer guard clauses and early returns.

### Branching

* Avoid large conditional trees.
* Prefer:

  * Polymorphism
  * Strategy pattern
  * Decomposition

### Cyclomatic Complexity

* Target: ≤ 10 per function.
* High-complexity functions must be refactored.

---

## 1.7 Explicit Error Handling

* No silent failures.
* No empty catch blocks.
* All external calls must:

  * Handle failure explicitly.
  * Provide contextual error information.
* Fail fast on invariant violations.

---

## 1.8 Dependency Discipline

* Dependencies must be injected, not hidden.
* No implicit globals.
* Infrastructure must not leak into domain logic.
* Core logic must be testable without:

  * Network
  * Database
  * External systems

Architecture must enable isolation.

---

# 2. Architecture Standards

All Software Architecture Documents (SAD) must:

* Clearly define module boundaries.
* Separate domain, application, and infrastructure layers.
* Identify complexity risk areas.
* Define error handling strategy.
* Define dependency direction.
* Explicitly describe test seams.

Architectural drift is not permitted without SAD revision.

---

# 3. Test Design Standards

## 3.1 Behavior-Focused Tests

Test names must describe behavior:

```
shouldRejectInvalidToken()
shouldRetryOnTransientFailure()
```

Avoid implementation-focused test names.

---

## 3.2 Coverage Requirements

All features must include tests covering:

* Happy path
* Failure path
* Edge cases

---

## 3.3 Test Isolation

* Tests must not require external infrastructure.
* Mock only at boundaries.
* Avoid testing private implementation details.
* Refactoring without behavior change must not break tests.

Tests validate behavior, not structure.

---

# 4. Implementation Standards

## 4.1 Refactor Before Extend

If existing code violates these standards:

1. Refactor first.
2. Then implement new functionality.

Do not stack features onto structural debt.

---

## 4.2 No Premature Optimization

* Avoid speculative caching or concurrency.
* Optimize only when measurable constraints exist.

---

## 4.3 Security Baseline

All code must:

* Validate external inputs.
* Avoid string concatenation for:

  * SQL
  * Shell execution
  * File paths
* Use parameterized queries where applicable.
* Never log secrets or sensitive information.

Security violations are automatic blockers.

---

## 4.4 Logging Standards

* Prefer structured logging.
* Include contextual identifiers (requestId, correlationId).
* Avoid noisy logs in tight loops.
* Logs must support debugging without exposing sensitive data.

---

# 5. Red Flag Patterns (Prohibited)

The following are not permitted without explicit justification:

* God classes
* 200+ line functions
* Deeply nested conditionals
* Boolean flags controlling multi-behavior flows
* Hard-coded environment values
* Copy-pasted blocks
* Silent error swallowing
* Mixing business and infrastructure logic
* “TODO” without issue reference

If unavoidable:

* Justification must be documented.
* Mitigation plan required.
* Technical debt explicitly recorded.

---

# 6. Refactor Triggers

Refactoring is mandatory if:

* Function exceeds 50 lines.
* Nesting depth exceeds 3.
* Logic appears in 2+ places.
* Tests require heavy mocking to function.
* Infrastructure and business logic mix.
* Branching complexity grows significantly.

Structural decay must not accumulate.

---

# 7. AI-Assisted Development Rules

When generating code via AI tools:

* The AI must prioritize structural integrity.
* The AI must not comply with requests that violate standards without proposing safer alternatives.
* The AI must flag quality degradation.
* The AI must recommend refactoring when necessary.

Functionality alone is insufficient for approval.

---

# 8. Definition of Done — Engineering Addendum

A change is not complete unless:

* Architecture aligns with these standards.
* Tests cover core behavior and failure modes.
* Code complexity remains within limits.
* No duplication introduced.
* Security baseline satisfied.
* Validators return PASS.

Deployment does not override quality gates.

---

# 9. Governance & Enforcement

These standards are enforced through:

* ACF and DCF (translate principles into enforceable guardrails)
* Artifact validators (PRD, SAD, TDD, WDD validators)
* Review prompt (checks code against ACF security guardrails and DCF quality bars)
* CI pipeline enforcement

Standards are policy, not suggestion.

---

# 10. Guiding Principle

Preventing structural degradation is cheaper than correcting it later.

Quality must be verified:

* Before code exists (architecture)
* Before behavior is implemented (TDD)
* Before deployment (code validation)

No stage advances on assumption.

