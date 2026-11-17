# PRD — Internal Link Shortener

## Document Control

| Field | Value |
|-------|-------|
| Artifact ID | PRD-THROWAWAY-INITIATIVE-001 |
| Owner | Todd Linnertz |
| Status | FROZEN |
| Last Validation | PASS |
| Frozen By | Todd Linnertz |
| Frozen Date | 2026-07-14 |

> Seeded fixture for the v1.3 three-way + real-AI validation. Hand-authored, not
> AI-generated: it exists to give the SAD generation a frozen upstream input,
> which is what the dark factory's autonomous path actually consumes. Small on
> purpose — it is context for every model call.

## 1. Problem

Internal teams paste long, unstable URLs (dashboards, runbooks, build logs) into
chat and docs. The links break when systems move, and nobody can tell which
links are still live. There is no internal way to mint a short, stable link or
to see whether a link still resolves.

## 2. Users

- **Engineers** who share dashboard and runbook links in chat and incident channels.
- **Platform owners** who move services and need existing links to keep working.

## 3. Goals

1. Mint a short link for any internal URL in under five seconds.
2. Let the target of a short link be changed without changing the short link.
3. Report whether each short link's target currently resolves.

## 4. Non-goals

- Public or anonymous link shortening. Internal, authenticated use only.
- Click analytics beyond a resolve count. No per-user tracking.
- Custom vanity domains.

## 5. Requirements

| ID | Requirement |
|----|-------------|
| R-1 | An authenticated user can submit a target URL and receive a short link. |
| R-2 | A short link resolves to its current target with an HTTP redirect. |
| R-3 | The owner of a short link can update its target; the short link is unchanged. |
| R-4 | The system records a resolve count per short link. |
| R-5 | A background check records, per link, whether the target last resolved. |
| R-6 | Only the link owner or a platform admin can update or delete a link. |

## 6. Constraints and assumptions

- Internal network only, behind existing SSO. No public exposure.
- Expected scale is modest: low thousands of links, low hundreds of resolves per minute.
- Must run on the existing Kubernetes platform. No new datastore technology.

## 7. Success measures

- Time to mint a link is under five seconds at the 95th percentile.
- Redirect latency is under 100ms at the 95th percentile.
- Zero broken links caused by a target move, once the owner updates the target.
