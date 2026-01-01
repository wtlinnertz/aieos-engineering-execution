# CONTRIBUTING

Thank you for your interest in contributing to **ai-sdlc-kit**.

This repository is a **public, employer-neutral toolkit** for AI-assisted SDLC artifacts, validators, and workflows. Contributions are welcome, provided they follow the rules below.

---

## Guiding Principles

All contributions must uphold these principles:

- **AI generates, validators decide**
- **Artifacts are promoted, not rewritten**
- **Execution artifacts are stricter than design artifacts**
- **Ambiguity is a failure condition**
- **Anonymization is mandatory**

If a contribution weakens these principles, it will not be accepted.

---

## What You Can Contribute

We welcome contributions in the following areas:

### Documentation
- Clarifications or improvements to existing docs
- New explanations that improve understanding
- Diagrams that reflect the documented flow

### Templates
- Improvements to existing artifact templates
- New templates that align with the playbook
- Template refinements that improve AI safety

### Validators
- Validator improvements that:
  - Reduce false positives
  - Improve determinism
  - Strengthen scope enforcement
- New validators that fit the artifact promotion model

### Examples
- Anonymized examples demonstrating correct usage
- End-to-end walkthroughs
- Validator PASS/FAIL examples

---

## What You Should NOT Contribute

The following will be rejected:

- Employer-specific content
- Proprietary system names or workflows
- Tool-specific implementations disguised as standards
- Large architectural rewrites without discussion
- Execution logic or scripts (this repo is process-focused)
- AI prompts that redesign artifacts instead of validating them

---

## Anonymization Requirements (MANDATORY)

All contributions must comply with **`ANONYMIZATION.md`**.

Before submitting a PR, confirm:
- No company names or internal acronyms appear
- No internal URLs, domains, or identifiers appear
- All examples use approved placeholders
- No screenshots or logs expose identifiers

Violations may result in immediate rejection or removal.

---

## Contribution Workflow

### 1. Fork and Branch
- Fork the repository
- Create a branch from `main`
- Use a descriptive branch name:
  - `docs/…`
  - `template/…`
  - `validator/…`
  - `example/…`

---

### 2. Make Your Changes
- Keep changes **small and focused**
- One logical improvement per PR
- Do not bundle unrelated changes

---

### 3. Validate Your Contribution
Before opening a PR, ensure:
- Content aligns with existing terminology (`TERMS.md`)
- Examples follow anonymization rules
- Templates and validators align with the playbook
- No contradictions or scope creep introduced

---

### 4. Open a Pull Request
Your PR description should include:
- What problem this change solves
- Which documents are affected
- Any intentional trade-offs
- AI Usage Disclosure.

PRs without a clear purpose may be closed.

---

## Review Expectations

Maintainers will review contributions for:

- Alignment with playbook principles
- Clarity and determinism
- Anonymization compliance
- Appropriateness for a public, reusable kit

Maintainers may request changes or reject PRs that:
- Introduce ambiguity
- Expand scope without justification
- Embed organization-specific assumptions

---

## Style & Formatting

- Use Markdown
- Prefer bullet points over long prose
- Keep language precise and unambiguous
- Avoid marketing language
- Favor enforceable rules over guidance where possible

---

## AI Usage in Contributions

You may use AI tools to assist in drafting content, provided that:

- You review all AI-generated output
- You ensure anonymization compliance
- You do not paste proprietary material into AI tools
- You take responsibility for the final content

---

## Reporting Issues

If you find:
- Anonymization violations
- Ambiguities in templates or validators
- Inconsistencies across documents

Please open an issue with a clear description and reference to the affected file(s).

---

## Code of Conduct

This project follows the **Code of Conduct** defined in `CODE_OF_CONDUCT.md`.

---

## Final Note

This repository prioritizes **clarity, safety, and reuse** over speed or novelty.

If you are unsure whether a contribution fits, open an issue first and start a discussion.

We value thoughtful, disciplined contributions that improve the system as a whole.

