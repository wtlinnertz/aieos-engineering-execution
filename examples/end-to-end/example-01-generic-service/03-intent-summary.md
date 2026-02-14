# Intent Summary (Approved)

- Provide read-only access to reference data via a single internal API
- Serve internal service consumers and automated batch processes only
- No UI, no data mutation, no external exposure
- Must follow standard platform guardrails (ACF-EX-001)
- Architecture should be simple: stateless API backed by a data store
- Must support rollback, structured logging, and health checks
- Reference data volume is modest; eventual consistency is acceptable
- Service-to-service authentication required for all consumers
