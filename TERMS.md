# TERMS

This document defines common terms used throughout the **ai-sdlc-kit** repository.
These definitions are intentionally **tool-agnostic**, **employer-neutral**, and **AI-friendly**.

---

## Core Concepts

### SDLC (Software Development Lifecycle)
The end-to-end process of delivering software, from intent and requirements through design, implementation, testing, deployment, and operation.

---

### AI-Assisted Delivery
The use of AI systems to **draft, decompose, validate, and analyze** SDLC artifacts.
AI may generate content, but **humans approve and decide**.

---

### Artifact
A persisted document produced at a specific stage of the SDLC.
Artifacts are **promoted**, not rewritten, as work progresses downstream.

Examples: PRD, SAD, TDD, WDD.

---

### Artifact Promotion
The formal progression of an artifact to the next stage after passing validation.
Once promoted, an artifact is considered **frozen**.

---

### Freeze
A state indicating an artifact is approved and may not be reinterpreted, expanded, or redesigned by downstream artifacts without explicit re-entry.

---

## Requirements & Architecture Artifacts

### PRD (Product Requirements Document)
Defines **what problem is being solved** and **why**, including:
- Goals and success criteria
- Functional and non-functional requirements
- Scope and non-goals
- Constraints and assumptions

The PRD does **not** define architecture or implementation.

---

### ACF (Architecture Context File)
Defines **enterprise-level architectural guardrails**, including:
- Platform constraints
- Security and compliance expectations
- Approved patterns and forbidden approaches

The ACF constrains architecture but does not describe a specific system.

---

### SAD (System Architecture Design)
Defines the **system-level architecture** for a specific solution, including:
- System boundaries
- Major components
- Architectural decisions
- Cross-cutting concerns

The SAD describes **system shape**, not implementation details.

---

### ADR (Architecture Decision Record)
A short record documenting a significant architectural decision, including:
- Decision made
- Rationale
- Alternatives considered
- Consequences

ADRs are referenced by the SAD.

---

## Design & Execution Artifacts

### DCF (Design Context File)
Defines **design standards and expectations** that constrain technical design, such as:
- Design principles
- Operational expectations
- Required quality bars

The DCF guides **how design is produced**, not what is built.

---

### TDD (Technical Design Document)
Defines a **buildable technical design**, including:
- Interfaces and contracts
- Build and deployment approach
- Failure and rollback behavior
- Test strategy

A TDD must be complete and deterministic before work decomposition begins.

---

### WDD (Work Design Document)
Breaks a frozen TDD into **atomic, executable work items**, each with:
- A single outcome
- Clear inputs and outputs
- Explicit scope and non-goals
- Acceptance criteria

WDD items are intended to map cleanly to Jira stories.

---

### Jira Story
A unit of execution derived from a WDD item.
A Jira story must meet the **Definition of Ready (DoR)** before execution.

---

## Validation & Governance

### Validator
A strict, non-prescriptive quality gate that evaluates whether an artifact is ready to be promoted.
Validators **do not redesign or suggest solutions**.

---

### Definition of Ready (DoR)
A set of criteria that must be met before a Jira story is eligible for execution.
The DoR ensures stories are:
- Atomic
- Executable
- Unambiguous
- AI-safe

---

### Intent Summary Pre-Pass
A mandatory, **ephemeral alignment step** run before generating SAD, TDD, or WDD artifacts.
The AI summarizes intent, constraints, and non-goals to confirm understanding **before** generation.

The Intent Summary is **not** a persisted artifact.

---

### Non-Goals
Explicitly stated exclusions that define what an artifact or work item does **not** cover.
Non-goals are enforceable and block downstream scope expansion.

---

### Granularity
The level of detail and size of a work item.
In this kit, executable work is expected to be:
- Single-outcome
- Single-repo/component
- Single-PR feasible
- Small enough to complete quickly

---

## Execution Roles

### AI Agent
An AI system acting as an execution participant (e.g., code generation, analysis).
AI agents **do not make final decisions**.

---

### Human
A person responsible for approval, judgment, and exception handling.

---

## Key Principles (Terminology Anchors)

- **AI generates, validators decide**
- **Artifacts are promoted, not rewritten**
- **Execution artifacts are stricter than design artifacts**
- **Ambiguity is a failure condition**

---

## Notes

- These terms are intentionally generic.
- No terminology implies a specific employer, vendor, or tool.
- Examples in this repository use placeholder names and neutral contexts.
