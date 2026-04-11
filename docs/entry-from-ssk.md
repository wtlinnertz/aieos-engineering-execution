# Entry from Solution Sourcing Kit (SSK)

**You are here because:** SSK sourcing evaluation is complete. The Sourcing Decision Record (SDR) is frozen and the Discovery PRD (DPRD) remains frozen from PIK.

**What you must bring:**

| Artifact | Status Required | Where to Find It |
|----------|----------------|-----------------|
| Discovery PRD (DPRD-{PROJECT}-{NNN}) | Frozen | PIK delivery location or `docs/sdlc/` in the consuming project |
| Sourcing Decision Record (SDR-{PROJECT}-{NNN}) | Frozen | SSK delivery location in the consuming project |
| Work Classification Record (WCR) | Frozen | PIK `docs/artifacts/` in the consuming project |
| Experiment Log (EL) | Frozen | PIK `docs/artifacts/` in the consuming project — confirm "proceed" decision |

**First artifact to generate in this kit:** Kit Entry Record (KER) — human-authored, no prompt

**Where to start:** `docs/session-setup.md` → "Kit Entry Record (KER)"

**What changes at this boundary:**

- You shift from sourcing evaluation to implementation. The "what" and "how to source" are settled; now you define "how to build/integrate/configure" in engineering terms.
- The DPRD becomes the PRD — placed, not regenerated, the same as a direct PIK → EEK handoff (Path A).
- The SDR provides sourcing context that shapes EEK scope:
  - **Build** (full EEK scope): Standard Path A flow. The SDR documents why Build was chosen; the KER references the SDR.
  - **Buy** (reduced scope): EEK focuses on integration and customization of the purchased solution. SAD and TDD scope accordingly — the purchased component is an external dependency, not a component to design.
  - **Adopt** (reduced scope): EEK focuses on integration and configuration of the adopted solution (e.g., open-source). Similar to Buy but with different risk and maintenance considerations documented in the SDR.
- The KER must reference both the DPRD ID and the SDR ID.

**What to verify before proceeding:**

| Check | How |
|-------|-----|
| DPRD is frozen | Confirm freeze status — same check as direct PIK → EEK handoff |
| SDR is frozen | Confirm SDR freeze status and that the SDR references the correct DPRD ID |
| SDR decision is clear | The SDR must contain a definitive Build, Buy, or Adopt decision with rationale |
| Scope alignment | For Buy/Adopt decisions, confirm the SDR defines what EEK is responsible for (integration boundary, customization scope) |

**How the SDR affects EEK artifacts:**

| EEK Artifact | Impact |
|-------------|--------|
| KER | References both DPRD and SDR. Path A entry. Notes sourcing decision. |
| PRD | Placed from DPRD (unchanged). Scope interpretation may narrow for Buy/Adopt based on SDR integration boundary. |
| ACF | References SDR for sourcing constraints. Buy/Adopt decisions add vendor or adoption constraints. |
| SAD | For Buy/Adopt: purchased/adopted component is an external dependency in the architecture, not a designed component. |
| TDD | For Buy/Adopt: design focuses on integration points, adapters, and configuration rather than core implementation. |
| WDD | Work items scoped to the EEK-responsible boundary defined by the SDR. |

**Common mistakes at this transition:**

| Mistake | How to Avoid |
|---------|--------------|
| Treating Buy/Adopt as full Build scope | Read the SDR integration boundary. EEK designs only the integration and customization layer, not the purchased/adopted solution itself. |
| Not referencing the SDR in the KER | The KER must explicitly reference the SDR ID and the sourcing decision. |
| Ignoring SDR constraints in ACF | The SDR's vendor constraints, licensing terms, and integration requirements belong in the Architecture Context File. |
| Running the PRD prompt on DPRD content (regenerating it) | Path A places the DPRD as `docs/sdlc/01-prd.md` without modification. Any errors return to PIK for correction. |

**If you arrived here without a complete upstream artifact:**

Stop. Both the DPRD and SDR must be frozen before EEK can begin. If the SDR is missing or unfrozen, return to SSK and complete the sourcing evaluation. If the DPRD is missing or unfrozen, return to PIK. EEK cannot begin without valid upstream artifacts.

---

*For the full entry flow, see `docs/playbook.md`.*
