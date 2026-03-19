# Engineering Standards

Version: v1.1

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

### Narrative Code Documentation

Beyond self-documenting names and extraction, code must carry narrative documentation that preserves design knowledge for future readers. The codebase itself is the living documentation of system behavior and design decisions.

**Module-level narrative (required for every non-trivial module):**

Every service, orchestrator, state machine, or complex utility must open with a narrative block explaining:

* What problem this module solves and its role in the larger system
* Key invariants or constraints that govern its behavior
* Dependencies and integration points

Example:

```
/**
 * Deployment Orchestrator
 *
 * Coordinates progressive delivery using GitOps principles. Determines
 * when a deployment can safely advance from canary to full rollout.
 *
 * Key constraints:
 * - All security gates must pass before promotion
 * - Observability signals must remain within SLA thresholds
 * - Rollbacks occur automatically if health signals degrade
 */
```

**Intent comments on non-obvious logic (required):**

When code makes a choice that is not self-evident from naming alone — retry counts, cache durations, algorithm selection, state machine transitions, parsing strategies — a comment must explain why that choice was made.

Example:

```
// We retry transient network failures because the upstream API
// occasionally drops connections under load. Three retries balances
// resiliency without causing excessive delays.
retries += 1
```

Example:

```
// We use a queue here instead of direct API calls. Earlier versions
// used synchronous requests, but that created cascading failures
// when the identity service slowed down. The queue isolates that
// dependency and improves resilience.
enqueue_identity_sync(user)
```

**Tradeoff and alternative comments (required when applicable):**

When a design choice involved evaluating alternatives, document what was considered and why the current approach won. This prevents future engineers from re-evaluating the same options.

**What does NOT qualify as narrative documentation:**

* Restating what the code does (`// increment counter` above `counter++`)
* Describing syntax (`// loop over items`)
* Repeating a function name in sentence form (`// validates the token` above `validateToken()`)

The test is: would a competent engineer joining this codebase understand *why* the code is written this way, not just *what* it does? If not, narrative documentation is missing.

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

### Dependency Direction Rule

Source code dependencies must point inward — from infrastructure toward domain, never the reverse.

A system's components are organized into architectural layers. The dependency direction rule constrains which layers may reference which:

* **Domain layer** (entities, business rules, value objects) — depends on nothing. No imports from application, infrastructure, or framework code.
* **Application layer** (use cases, orchestration, service interfaces) — depends on domain only. Defines interfaces (abstractions) for capabilities it needs from the outer layers.
* **Infrastructure layer** (database adapters, API controllers, framework bindings, external service clients) — depends on application and domain. Implements the interfaces defined by the application layer.

This means:

* A domain entity must never import a database client, HTTP framework, or external SDK.
* An application service defines an interface (e.g., `UserRepository`); the infrastructure layer provides the implementation (e.g., `PostgresUserRepository`).
* Framework-specific annotations, decorators, or base classes must not appear in domain or application layers.
* If a dependency direction violation is unavoidable, it must be documented with justification and a mitigation plan (e.g., anti-corruption layer).

The dependency direction rule is enforced through:
* SAD §4 layer assignment (each component mapped to domain, application, or infrastructure)
* TDD §3 technical overview (layer assignment carried forward with dependency constraints)
* Execution Phase 4 Review (dependency direction violations are blockers)

---

## 1.9 Backward Compatibility

Changes to existing code must not break callers that depend on current behavior. This applies to all interfaces — public APIs, inter-service contracts, shared library functions, event schemas, and any surface consumed by code outside the immediate change scope.

### What Constitutes a Breaking Change

A change is breaking if any existing caller would fail, produce incorrect results, or require modification after the change is deployed:

* Removing or renaming a public function, method, endpoint, or field
* Changing a function signature (parameters, return type, exception types)
* Changing observable behavior that callers depend on (response codes, error formats, event payloads, ordering guarantees)
* Tightening input validation (rejecting inputs previously accepted)
* Loosening output guarantees (returning nulls where non-null was guaranteed)
* Changing default values that callers rely on implicitly
* Removing or reordering fields in serialized formats (JSON, protobuf, database schemas)

### What Is Not a Breaking Change

* Adding new optional parameters with backward-compatible defaults
* Adding new fields to response payloads (when consumers ignore unknown fields)
* Adding new endpoints or methods
* Relaxing input validation (accepting inputs previously rejected) — provided the output remains consistent
* Performance improvements that do not alter observable behavior
* Internal refactoring that preserves all public contracts

### When Breaking Changes Are Acceptable

Breaking changes are not prohibited — they are governed. A breaking change is acceptable when:

1. The TDD §4 interface contract explicitly classifies the interface as `evolving` or `experimental`
2. The change is documented in the work item with justification and impact scope
3. All known consumers are identified and a migration path is provided
4. The execution plan accounts for consumer updates as part of the work

Breaking changes to interfaces classified as `locked` in TDD §4 require TDD re-entry and re-freeze before implementation.

### Verification Standard

Backward compatibility is not assumed — it is proven by test. For any work item that modifies an existing interface:

* **Contract preservation tests** must be written in Phase 1 (Tests) before any code changes
* These tests exercise the existing interface's current behavior from the caller's perspective
* They must pass before implementation begins (establishing a baseline) and continue to pass after implementation is complete
* If a contract preservation test must change to accommodate the new behavior, that change is evidence of a breaking change and must be explicitly justified

The contract preservation test requirement is enforced through `execution-spec.md` Phase 1 and verified in Phase 4 Review.

### Enforcement

| Where | What |
|-------|------|
| TDD §4 | Interface stability classification (locked / evolving / experimental) |
| Execution-spec Phase 1 | Contract preservation tests required for work items modifying existing interfaces |
| Execution-spec Phase 4 | Review check verifies contract preservation tests exist and pass |
| WDD Interface Contract Reference | Identifies which interfaces each work item touches |

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
* Pin all dependencies to explicit versions — unpinned or floating version ranges are not permitted.
* Never implement custom cryptographic algorithms. Use platform-approved libraries only. Deprecated algorithms (MD5, SHA-1, DES, RC4) are prohibited.
* Document any deviation from these controls with a risk assessment and expiration date — undocumented security exceptions are automatic blockers.

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

---

## Enforcement Mapping

| Rule Category | Enforced By |
|---------------|-------------|
| Core engineering doctrine (readability, SRP, naming, DRY, complexity, error handling, dependencies) (§1) | `acf-spec.md` §security_guardrails hard gate; `dcf-spec.md` §design_principles hard gate; `wdd-spec.md` §granularity hard gate |
| Architecture standards — module boundaries, layer separation, dependency direction (§2, §1.8) | `sad-spec.md` §layer_assignment hard gate; `sad-spec.md` §intent_integrity hard gate; `tdd-spec.md` §intent_alignment hard gate |
| Test design standards — behavior-focused, coverage, isolation (§3) | `dcf-spec.md` §testing_expectations hard gate; `dor-spec.md` §testability hard gate |
| Security baseline — input validation, parameterized queries, dependency pinning, no custom crypto (§4.3) | `acf-spec.md` §security_guardrails hard gate |
| Definition of Done — engineering addendum (§8) | `dor-spec.md` §definition_of_done hard gate |
| Governance & enforcement model (§9) | `acf-spec.md` and `dcf-spec.md` translate these principles into enforceable guardrails reviewed by all downstream validators |

| Narrative code documentation — module narratives, intent comments, tradeoff preservation (§1.4) | `execution-spec.md` Phase 4 Review Check 15 (narrative_documentation); `dcf-spec.md` §design_principles hard gate |
| Backward compatibility — contract preservation, breaking change governance (§1.9) | `execution-spec.md` Phase 1 contract preservation tests; `execution-spec.md` Phase 4 Review Check 17 (backward_compatibility); TDD §4 stability classification |

Red flag patterns (§5) and AI-assisted development rules (§7) are advisory — reinforced by generation prompts but not directly evaluated by a single hard gate.
