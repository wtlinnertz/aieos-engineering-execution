# CI Spec Authoring Prompt

Use this prompt to walk a developer through tailoring
`templates/ci-spec/python-k8s-flux.yaml` to their service. It is an
interview, not a form — ask one question at a time, accept the developer's
answer, update the draft in memory, then move to the next. Do not present
the template verbatim and ask "any changes?" — the walkthrough's value is
surfacing decisions the developer otherwise wouldn't think to make.

## When this prompt runs

- Trigger: after the ORD is frozen in Layer 4 (Engineering Execution).
- Input artifacts: frozen ORD, target repo reference, developer availability.
- Output: a draft CI spec committed to the target repo at `.aieos/ci.spec.yaml`.
  The draft is then validated against `schema/ci-spec.schema.json` from
  `aieos-governance-foundation` and cross-checked via the pipeline runner's
  spec validator (M3.2) before freeze.

## Invariants

- Every action referenced must exist in `aieos-governance-foundation` at
  `taxonomy/actions-v1.md` (frozen at `v1.0-taxonomy`).
- Every action's `adapter_preferences` entry must be a registered adapter
  for that action in the harness registry. The resolver refuses ambiguous
  or unresolvable bindings.
- The `depends_on` graph must be a DAG. The template's default graph is
  correct for most Python-K8s-Flux services; justify any edge changes.
- Criteria are tool-agnostic. Do not reference specific scanner rule names
  or tool flags — those are adapter-side concerns.

## Interview

Ask these in order. Skip a question only when the developer's prior answer
makes it clearly redundant.

### Coverage threshold

"`test.unit` defaults to `min_coverage: 80`. Is your service currently
above or below 80% coverage? If below, we either keep 80 (aspirational —
the pipeline fails until you catch up) or lower the threshold to your
current level and track the gap as technical debt."

Record: `actions[test.unit].criteria.min_coverage: <value>`.

### Integration test scope

"Does your service have integration tests? If yes, do they use
containerized dependencies (docker-compose, testcontainers) or hit live
shared services? If neither, we remove `test.integration` from the spec."

Record: keep/drop `test.integration`; if kept, capture any
`dependency_fixtures` config.

### SAST severity threshold

"`security.sast` defaults to `max_severity: high`. That lets `critical`
findings fail the build. Acceptable?"

Record: `actions[security.sast].criteria.max_severity: <value>`.
Lower (`medium`) is stricter; `critical` allows `high` findings to pass.

### SCA severity + CVSS threshold

"`security.sca` defaults to `max_severity: high` and `max_cvss: 7.0`.
CVSS 7.0 corresponds to the low-end of `high`. Keep the double check, or
rely on severity alone?"

Record: choose one or both. Remove `max_cvss` if severity is sufficient.

### Secret-scan stance

"`security.secret-scan` defaults to `expect_zero_findings: true`. That
means any detected secret fails the build. Acceptable, or do you have
allowlisted legitimate-looking patterns (test fixtures, sample keys)?"

Record: keep/drop, or switch to `max_severity: high` with an allowlist
documented in the adapter's config.

### Build artifact type

"The spec assumes a single OCI image via buildah. Is that correct for
your service? Alternatives: multiple images, a binary package, a tarball."

Record: keep `build.artifact` as-is, or note alternatives (which require
contract extensions beyond v1).

### Container-scan threshold

"`security.container-scan` defaults to `max_severity: high`. Container
images often carry CVEs that cannot be fixed quickly (transitive OS
packages). Consider: keep high but document tolerated CVEs in an ignore
list, or loosen to `critical` and audit in periodic reviews."

Record: threshold + ignore-list reference.

### SBOM format

"`sbom.generate` defaults to CycloneDX. SPDX 2.3 is an accepted
alternative. Any downstream consumers (procurement, legal) require a
specific format?"

Record: `format_preference`.

### Sign-identity mode

"`sign.artifact` and `sign.attestation` default to `ambient` OIDC (GitHub
Actions). Confirm your CI runs on a GHA workflow that carries
`id-token: write`. Otherwise, we switch to keypair mode and need to decide
where the private key lives."

Record: `signing_identity_ref` in adapter config.

### Publish destination

"`publish.artifact` requires a destination registry URL. Where does your
artifact live? Common: GHCR, Harbor, Artifactory. Include the repository
path (e.g., `ghcr.io/org/app`)."

Record: `destination_registry_url` + `destination_repository_path`.

### Timeouts and retries

"Default `timeout_seconds: 1800` (30 min). Retries: `max_retries: 1`
with exponential backoff. Right for your pipeline?"

Record: `policies.timeout_seconds`, `policies.retry`.

### Adapter preferences

"The template names all ten v1 adapters. If your org has a preferred
alternate (e.g., trufflehog instead of semgrep for secret-scan), call it
out. The resolver refuses ambiguous choices — every action must resolve
to exactly one adapter."

Record: `policies.adapter_preferences` overrides.

## Validation + freeze

After the walkthrough:

1. Write the draft to `<target-repo>/.aieos/ci.spec.yaml`.
2. Run the pipeline runner's spec validator:
   ```bash
   aieos-pipeline-runner run --spec .aieos/ci.spec.yaml --expected-hash <sha> \
     --use-mock-adapters  # dry-run against mocks first
   ```
3. Fix any FAIL checks the validator reports.
4. When all checks PASS on mocks, freeze: commit `.aieos/ci.spec.yaml`
   and cache it in the artifact store keyed by the commit SHA.
5. Schedule a human review per the framework's two-person sign-off rule.

The runner refuses unfrozen specs at execution time, so freeze-before-
promote is enforced by the tool, not by convention.
