# ACF — Architecture Context File — Internal Link Shortener

## Document Control

| Field | Value |
|-------|-------|
| Artifact ID | ACF-THROWAWAY-INITIATIVE-001 |
| Owner | Todd Linnertz |
| Status | FROZEN |
| Last Validation | PASS |
| Frozen By | Todd Linnertz |
| Frozen Date | 2026-07-14 |

> Seeded fixture for the v1.3 three-way + real-AI validation. Hand-authored.
> `sad-prompt.md` requires a frozen PRD *and* a frozen ACF; this supplies the
> architectural guardrails the SAD must respect.

## 1. Architectural guardrails

- **Onion / dependency direction.** Domain logic depends on nothing outward.
  Adapters (HTTP, persistence) depend inward. No domain import of a framework type.
- **Stateless services.** All service instances are interchangeable. Any state
  lives in the datastore, never in process memory.
- **One datastore.** PostgreSQL is the only permitted persistent store. No new
  database technology may be introduced by this initiative.
- **Synchronous request path.** The redirect path must not depend on a queue or
  any asynchronous hop.

## 2. Permitted patterns

- Repository pattern for persistence access.
- Read-through cache for the resolve path, provided a cache miss still resolves
  correctly against PostgreSQL.
- Background workers for periodic target-health checks, isolated from the
  redirect path.

## 3. Prohibited patterns

- Shared mutable in-process state across requests.
- Direct SQL in HTTP handlers.
- Introducing a message broker, a second datastore, or a new runtime language.
- Any public, unauthenticated ingress.

## 4. Organization-level constraints

- Runs on the existing Kubernetes platform, deployed via the standard GitOps flow.
- Authentication is delegated to the existing SSO provider. The service never
  stores credentials.
- All configuration arrives via environment variables. No secrets in images.
- Observability uses the platform's existing OpenTelemetry collector.

## 5. Integration points

- **SSO provider** — authenticates every request; the service consumes identity only.
- **PostgreSQL** — the system of record for links, owners, and resolve counts.
- **OpenTelemetry collector** — receives traces and metrics.
