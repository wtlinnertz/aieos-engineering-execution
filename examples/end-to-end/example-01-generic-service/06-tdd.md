# TDD — Reference Data Service

## 0. Document Control
- System/Component Name: Reference Data Service
- TDD ID: TDD-EX-001
- Author: Example Team
- Date: 2025-01-25
- Status: Frozen
- Upstream Artifacts:
  - SAD: SAD-EX-001
  - ACF: ACF-EX-001
  - DCF: DCF-EX-001
- Related ADRs: None

## 1. Intent Summary
(Paste of approved intent summary — see `03-intent-summary.md`)

- Provide read-only access to reference data via a single internal API
- Serve internal service consumers and automated batch processes only
- No UI, no data mutation, no external exposure
- Must follow standard platform guardrails (ACF-EX-001)
- Architecture should be simple: stateless API backed by a data store
- Must support rollback, structured logging, and health checks
- Reference data volume is modest; eventual consistency is acceptable
- Service-to-service authentication required for all consumers

## 2. Scope and Non-Goals (Hard Boundary)

### In Scope
- Implement read-only API endpoint for retrieving reference data by key
- Integrate with a relational data store for reference data persistence
- Implement service-to-service authentication validation
- Implement structured logging for all requests
- Implement health check endpoint

### Explicit Non-Goals (must align with SAD)
- Write or mutation operations
- User interface
- External network exposure
- Data ingestion or pipeline management
- Caching beyond platform defaults

## 3. Technical Overview
The service is a stateless HTTP API that reads reference data from a relational data store and returns it to authenticated consumers. It runs as a container on the standard platform runtime.

## 4. Interfaces and Contracts (Hard)

### GET /reference-data/{key}

**Request:**
- Method: GET
- Path parameter: `key` (string, required)
- Headers: `Authorization` (service credential, required)

**Response (success):**
- Status: 200 OK
- Body: `{ "key": "<key>", "value": "<value>", "last_updated": "<ISO 8601>" }`

**Response (not found):**
- Status: 404 Not Found
- Body: `{ "error": "not_found", "message": "Key does not exist" }`

**Response (unauthorized):**
- Status: 401 Unauthorized
- Body: `{ "error": "unauthorized", "message": "Invalid or missing credentials" }`

**Response (server error):**
- Status: 500 Internal Server Error
- Body: `{ "error": "internal_error", "message": "Unable to retrieve data" }`

### GET /health

**Response:**
- Status: 200 OK
- Body: `{ "status": "healthy" }`
- Returns 503 if data store is unreachable

## 5. Build and Deployment Approach (Deterministic)

### Build
- Build container image from source
- Run unit tests during build; fail build if tests fail
- Push image to organization container registry

### Deployment
- Deploy via standard CI/CD pipeline
- Pipeline stages: Build → Test → Deploy to staging → Deploy to production
- Health check must pass within 30 seconds after deployment
- Rollback: Redeploy previous container image if health check fails

### Required Inputs
- Source code repository
- Container registry credentials (injected by pipeline)
- Data store connection string (injected via secrets manager)

## 6. Failure Handling and Rollback (Hard)

| Failure Mode | Impact | Detection | Mitigation |
|-------------|--------|-----------|------------|
| Data store unavailable | API returns 500 for all data requests | Health check returns 503 | Return clear error; do not crash. Consumers retry or degrade gracefully |
| Invalid credentials presented | Request rejected | Structured log entry with 401 status | Return 401; log the attempt |
| Deployment fails health check | New version does not serve traffic | Health check timeout (30s) | Automatic rollback to previous image |
| Key not found | Consumer receives 404 | Normal request flow | Return 404 with clear error message |

## 7. Observability (Hard)

### Logging
- Structured JSON logs for every request: method, path, status code, latency_ms, timestamp
- Error logs include error type and relevant context (no sensitive data)

### Metrics
- Request count (by status code)
- Error rate (4xx and 5xx)
- Latency percentiles (p50, p95, p99)

### Health Check
- `GET /health` returns 200 when healthy, 503 when data store is unreachable

## 8. Testing Strategy (Hard)

### Unit Tests
- Data access component: Verify correct data retrieval and error handling
- Authentication validation: Verify accept/reject logic
- Pass criteria: All unit tests pass
- Fail criteria: Any unit test fails

### Integration Tests
- End-to-end: Send authenticated request, verify correct response
- Not found: Send request for missing key, verify 404 response
- Unauthorized: Send request without credentials, verify 401 response
- Pass criteria: All integration tests pass
- Fail criteria: Any integration test fails

### Failure Test
- Data store unavailable: Verify service returns 500 and does not crash

## 9. Operational Notes (Minimum Runbook)
- Start: Deploy container via standard pipeline
- Stop: Remove from load balancer, then stop container
- Verify: Check `/health` endpoint returns 200
- Troubleshoot: Check structured logs for error entries; verify data store connectivity

## 10. Dependencies
- Platform container runtime
- Platform identity provider
- Data store (relational)
- Central log aggregator
- Organization container registry

## 11. Risks and Assumptions

| Risk | Impact | Mitigation |
|------|--------|------------|
| Data store schema changes | API may return incorrect data | Schema versioning; integration tests catch mismatches |

### Assumptions
- Data store schema is stable and managed externally
- Platform secrets manager is available at deployment time

## 12. Readiness Checklist (Self-Check)
- [x] Intent summary matches SAD
- [x] Non-goals are restated
- [x] Interfaces are explicit with schemas
- [x] Build and deployment are deterministic
- [x] Failure modes are defined
- [x] Testing strategy covers success and failure
- [x] No unresolved decisions remain

## 13. Freeze Declaration
This TDD is approved and frozen. WDD work may proceed.

- Approved By: Example Team
- Date: 2025-01-25
