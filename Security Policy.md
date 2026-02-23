# Security Policy

## Supported Content

**aieos-engineering-execution** is a documentation- and template-based repository.
It does **not** provide a running service, hosted application, or executable software.

Security reports should focus on:
- Accidental inclusion of sensitive or identifying information
- Violations of anonymization rules
- Unsafe guidance that could lead to security issues if followed
- Errors in security-related templates or validators

---

## Reporting a Security Issue

If you discover a potential security concern, please report it responsibly.

### Please report issues related to:
- Proprietary or employer-specific information accidentally included
- Credentials, secrets, tokens, or keys (even if partially redacted)
- Internal URLs, domains, or system identifiers
- Guidance that could encourage insecure practices
- AI prompts or examples that leak sensitive data

### How to report

- **Preferred:** Open a private GitHub Security Advisory for this repository  
  (Use GitHub’s “Report a vulnerability” feature if available.)
- **Alternative:** Open an issue labeled `security` **only if the issue does not expose sensitive data**.

Please **do not** include sensitive details directly in a public issue.

---

## What Not to Report

The following are **not** considered security issues for this repository:
- Requests for new features or templates
- Disagreements about process or design philosophy
- General documentation improvements
- Tooling or implementation vulnerabilities in downstream systems

---

## Response Process

Maintainers will:
1. Review the report
2. Determine whether it represents a valid security concern
3. Remove or correct affected content as needed
4. Coordinate disclosure if appropriate

Because this repository does not ship software artifacts, response timelines may vary.

---

## Responsible Disclosure

We ask that reporters:
- Avoid public disclosure until maintainers have responded
- Limit exposure of sensitive information
- Follow responsible disclosure practices

---

## Final Note

This repository prioritizes **safety, clarity, and reuse**.

If something could reasonably cause harm if copied into a real system, it’s worth reporting.

Thank you for helping keep **aieos-engineering-execution** safe and reusable.
