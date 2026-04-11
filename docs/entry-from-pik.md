# Entry from Product Intelligence Kit (PIK)

**You are here because:** PIK discovery is complete and the Discovery PRD (DPRD) is frozen.

**What you must bring:**

| Artifact | Status Required | Where to Find It |
|----------|----------------|-----------------|
| Discovery PRD (DPRD-{PROJECT}-{NNN}) | Frozen | PIK delivery location or `docs/sdlc/` in the consuming project |
| Work Classification Record (WCR) | Frozen | PIK `docs/artifacts/` in the consuming project |
| Experiment Log (EL) | Frozen | PIK `docs/artifacts/` in the consuming project — confirm "proceed" decision |

**First artifact to generate in this kit:** Kit Entry Record (KER) — human-authored, no prompt

**Where to start:** `docs/session-setup.md` → "Kit Entry Record (KER)"

**What changes at this boundary:**

- You shift from problem framing and hypothesis validation to implementation intent. The "why" is settled; now you define "what" in engineering terms.
- The DPRD becomes the PRD — placed, not regenerated. Your first validation task is the acceptance check, not a generation task.
- The discovery team and the engineering team may be different people. The KER is the handoff acknowledgment.

**Common mistakes at this transition:**

| Mistake | How to Avoid |
|---------|--------------|
| Running the PRD prompt on DPRD content (regenerating it) | Path A places the DPRD as `docs/sdlc/01-prd.md` without modification. Any errors return to PIK for correction. |
| Skipping the DPRD consistency check | Run `docs/prompts/dprd-consistency-check-prompt.md` before the PRD acceptance check to confirm the DPRD was placed unmodified. |
| Creating the KER without consulting the WCR | The KER's path selection (Path A vs Path B) must match the WCR routing decision. |

**If you arrived here without a complete upstream artifact:**

Stop. Return to PIK, complete the discovery flow, freeze the DPRD, and then re-enter EEK. EEK cannot begin without a valid upstream artifact. If this initiative qualifies for Path B (well-understood scope, no discovery needed), the KER must document the justification explicitly — do not assume Path B is available without documenting the reason.

---

*For the full entry flow, see `docs/playbook.md`.*
