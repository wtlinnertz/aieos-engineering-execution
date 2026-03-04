# EEK Kit Entry — Specification

The Kit Entry Record is a lightweight gate that must be completed before beginning any artifact generation in the Engineering Execution Kit. It confirms the work is appropriately routed, the correct entry path is selected, and a priority decision exists on record.

This is a **boundary contract**, not a governed artifact. The record is human-authored. It is validated against this spec before the PRD entry path begins.

---

## Purpose

The kit entry spec serves two roles:

1. **Intake gate** — Confirms that work arriving at EEK has been deliberately routed, that the entry path is explicitly selected and justified, and that a priority decision exists; prevents unclassified or undirected work from entering the execution artifact chain
2. **Path record** — Freezes the entry path decision so it is traceable and auditable

---

## Upstream Dependencies

- Work Classification Record (frozen) from the Product Intelligence Kit — required if PIK was used; otherwise bypass justification is required
- Either a frozen DPRD (Path A) or a completed Product Brief (Path B)

---

## Required Sections

1. Document Control
2. Classification Check
3. Entry Path
4. Priority Decision
5. Scope Boundary
6. Freeze Declaration

---

## Content Rules

### Document Control
**Rules**
- Record ID must be present
- Date must be present
- A brief work summary must be present (1-2 sentences)

**Failure Examples**
- Missing Record ID
- Work summary absent

### Classification Check
**Rules**
- Must declare one of two states:
  - (a) A frozen Work Classification Record exists, its Record ID is referenced, and it routes to EEK; or
  - (b) No classification record exists, and a justification for the absence is documented
- Justification for absence must be specific — not a blank field or "N/A"
- If a classification record exists but routes to a different destination, this gate fails

**Failure Examples**
- Classification Record ID referenced but record routes to PIK, not EEK
- No classification record and no justification for its absence
- Field left blank

### Entry Path
**Rules**
- Exactly one path must be selected: Path A or Path B — not both, not neither
- **Path A** requires a reference to the frozen DPRD (document ID, file path, or link)
- **Path B** requires a documented justification for bypassing discovery (why PIK was not used)
- Path B justification must be specific — not "we decided to skip discovery" or similar
- Path B justification must not assert that discovery is simply unnecessary — it must explain the specific conditions that make direct entry appropriate

**Failure Examples**
- Neither path selected
- Path A selected but no DPRD reference provided
- Path B selected with justification "no time for discovery" — rationale, not justification
- Path B selected with justification "it's a small change" — scope claim, not justification

**Valid Path B Justifications**
- Bug fix with clear reproduction steps and no design decision required
- Tech debt item with engineering-defined scope and no user-facing change
- Compliance mandate with requirements already specified by a regulatory body

### Priority Decision
**Rules**
- Must confirm a priority decision exists on record
- Must provide a traceable reference — a date and approver name, a tracking system ID, or a reference to a planning session record
- "We decided it's a priority" without a reference is not sufficient
- Does not require a priority score, ranking, or justification — only that the decision is on record

**Failure Examples**
- "Yes, it's prioritized" with no reference
- Field blank or absent

### Scope Boundary
**Rules**
- Must state what this work covers and what it does not cover
- Must be specific enough to confirm the work is bounded — not "the full feature" or "everything in the brief"
- Need not be exhaustive — the PRD or DPRD carries the full scope; this is a boundary check, not a requirements statement

**Failure Examples**
- "Implement the notification feature" — no boundary
- "Everything in the PRD" — not a boundary statement
- Section absent or empty

---

## Format Requirements

- Classification Check must reference a specific Record ID or document a specific justification — not a checkbox alone
- Entry Path must clearly mark exactly one selection
- Priority Decision must include a traceable reference

---

## Completeness Rules

- All five substantive sections must be present and non-empty
- Classification Check must resolve to one of the two valid states
- Exactly one entry path must be selected
- Priority reference must be traceable

---

## Relationship Rules

- The Kit Entry Record gates the PRD Entry Path — no artifact generation may begin until the record is frozen and validated
- The Kit Entry Record does not replace the PRD Acceptance Check (Path A) or PRD Validator (Path B) — those follow after the entry gate passes
- The Kit Entry Record is not re-run when re-entering EEK under the Re-entry Protocol — it applies only to initial kit entry

---

## Hard Gates

1. **document_control** — Record ID, date, and work summary present
2. **classification_check** — Frozen classification record referenced (routing to EEK), or absence explicitly justified
3. **path_selected** — Exactly one entry path selected with required evidence (DPRD reference for Path A; specific justification for Path B)
4. **priority_on_record** — Priority decision confirmed with a traceable reference
5. **scope_bounded** — Work boundary stated with enough specificity to confirm it is bounded
