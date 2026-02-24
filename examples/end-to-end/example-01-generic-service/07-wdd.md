# WDD — Reference Data Service

## 0. Document Control
- WDD ID: WDD-EX-001
- Author: Example Team
- Date: 2025-01-28
- Status: Frozen
- Parent TDD:
  - TDD ID: TDD-EX-001
  - TDD Status: Frozen

## 1. Scope and Non-Goals (Copied from TDD)

### In Scope
- Implement read-only API endpoint for retrieving reference data by key
- Integrate with a relational data store for reference data persistence
- Implement service-to-service authentication validation
- Implement structured logging for all requests
- Implement health check endpoint

### Explicit Non-Goals
- Write or mutation operations
- User interface
- External network exposure
- Data ingestion or pipeline management
- Caching beyond platform defaults

## 2. Work Items

### WDD Item
- WDD Item ID: WDD-EX-001
- Parent TDD Section: Sections 4, 6 (Interfaces, Failure Handling)
- Assignee Type: Either
- Required Capabilities: backend, database, API-design, security
- Complexity Estimate: M — Multiple concerns (API, data access, auth) with external integration across 4 acceptance criteria, but uses well-established patterns.

#### Intent (1–2 sentences)
Implement the read-only API endpoint with data access layer and authentication validation, returning reference data by key with correct status codes.

#### In Scope
- GET /reference-data/{key} endpoint with response codes (200, 401, 404, 500)
- Data access component reading from relational data store
- Service-to-service authentication validation

#### Out of Scope / Non-Goals
- Write endpoints
- UI components
- External network configuration
- Health check endpoint (covered by WDD-EX-002)
- Structured logging (covered by WDD-EX-003)
- Container build and deployment (covered by WDD-EX-004)
- Test implementation (covered by WDD-EX-005)

#### Inputs
- TDD-EX-001 interface contracts (Section 4)
- Data store connection string (via secrets manager)
- Platform identity provider configuration

#### Outputs
- Functional API endpoint serving reference data by key with correct status codes

#### Acceptance Criteria (Executable)
- AC1: Given a valid key and valid credentials, when GET /reference-data/{key} is called, then the correct data is returned with status 200. Failure: If the data returned does not match the stored record or status is not 200, the criterion fails.
- AC2: Given an invalid key and valid credentials, when GET /reference-data/{key} is called, then a 404 error is returned. Failure: If any status other than 404 is returned for a non-existent key, the criterion fails.
- AC3: Given missing or invalid credentials, when GET /reference-data/{key} is called, then a 401 error is returned. Failure: If the request is processed or any status other than 401 is returned without valid credentials, the criterion fails.
- AC4: Given the data store is unavailable, when GET /reference-data/{key} is called, then a 500 error is returned and the service does not crash. Failure: If the service crashes, hangs, or returns a status other than 500 when the data store is unreachable, the criterion fails.

#### Definition of Done (Hard)
- [ ] PR merged
- [ ] Unit tests passing for data access and auth validation
- [ ] Integration tests passing for success, not-found, unauthorized, and failure scenarios
- [ ] Code reviewed

#### Dependencies
- Data store must be provisioned and accessible
- Platform identity provider must be configured

#### Rollback / Failure Behavior
- If data store is unavailable at runtime, API returns 500; service remains running
- Code rollback via git revert if defects are found post-merge

---

### WDD Item
- WDD Item ID: WDD-EX-002
- Parent TDD Section: Section 4 (Interfaces — health endpoint)
- Assignee Type: Either
- Required Capabilities: backend, API-design
- Complexity Estimate: S — Single concern with no item dependencies, 2 acceptance criteria, and straightforward implementation pattern.

#### Intent (1–2 sentences)
Implement the health check endpoint that reports service operational status.

#### In Scope
- GET /health endpoint returning status 200 when service is operational

#### Out of Scope / Non-Goals
- Application metrics emission (platform default)
- Structured request logging (covered by WDD-EX-003)
- Alerting setup

#### Inputs
- TDD-EX-001 interface contracts (Section 4 — GET /health)

#### Outputs
- Functional health check endpoint returning status 200

#### Acceptance Criteria (Executable)
- AC1: Given the service is running, when GET /health is called, then status 200 is returned with a valid health response body. Failure: If the health endpoint returns any status other than 200 or returns no body, the criterion fails.
- AC2: Given the data store is unavailable, when GET /health is called, then the response indicates degraded status. Failure: If the health endpoint reports healthy when a critical dependency is unreachable, the criterion fails.

#### Definition of Done (Hard)
- [ ] PR merged
- [ ] Unit tests passing for health endpoint
- [ ] Health endpoint manually verified in test environment
- [ ] Code reviewed

#### Dependencies
- WDD-EX-001 must be complete (health endpoint integrates with the same service)

#### Rollback / Failure Behavior
- If health check fails in production, deployment pipeline should trigger automatic rollback
- Code rollback via git revert if defects are found post-merge

---

### WDD Item
- WDD Item ID: WDD-EX-003
- Parent TDD Section: Section 7 (Observability)
- Assignee Type: Either
- Required Capabilities: backend, observability
- Complexity Estimate: S — Single middleware concern with 2 acceptance criteria and one dependency; well-understood logging pattern.

#### Intent (1–2 sentences)
Implement structured JSON request logging for all API requests to enable operational observability.

#### In Scope
- Structured JSON request logging middleware for all API calls
- Log fields: timestamp, request ID, method, path, status code, latency

#### Out of Scope / Non-Goals
- Application metrics emission (platform default)
- Log aggregator configuration
- Alerting setup

#### Inputs
- TDD-EX-001 observability requirements (Section 7)

#### Outputs
- Structured JSON logs emitted for all API requests

#### Acceptance Criteria (Executable)
- AC1: Given any API request is processed, when the response is sent, then a structured JSON log entry is emitted containing timestamp, request ID, method, path, status code, and latency. Failure: If any of the required log fields are missing or the log is not valid JSON, the criterion fails.
- AC2: Given a logging failure occurs, when an API request is processed, then the API response is not blocked or delayed. Failure: If a logging error causes the API to return an error or increases response latency beyond normal bounds, the criterion fails.

#### Definition of Done (Hard)
- [ ] PR merged
- [ ] Unit tests passing for logging middleware
- [ ] Structured log output verified in test environment
- [ ] Code reviewed

#### Dependencies
- WDD-EX-001 must be complete (logging middleware wraps the API endpoints)

#### Rollback / Failure Behavior
- Logging failures must not block API responses (fire-and-forget)
- Code rollback via git revert if defects are found post-merge

---

### WDD Item
- WDD Item ID: WDD-EX-004
- Parent TDD Section: Section 5 (Build and Deploy)
- Assignee Type: Human
- Required Capabilities: infrastructure, CI-CD, container
- Complexity Estimate: M — Moderate integration across 3 infrastructure dependencies with pipeline coordination; 3 acceptance criteria spanning build, deploy, and verification.

#### Intent (1–2 sentences)
Configure container build and CI/CD pipeline deployment for the Reference Data Service to the standard platform runtime.

#### In Scope
- CI/CD pipeline configuration for build, push, deploy, and health check verification (using standard platform base image and build tooling as defined in ACF)

#### Out of Scope / Non-Goals
- Application code changes
- Multi-environment promotion (single target environment)
- Infrastructure provisioning

#### Inputs
- TDD-EX-001 build and deploy requirements (Section 5)
- Container registry credentials (via pipeline)
- Platform runtime configuration

#### Outputs
- Service deployed and verified healthy on internal network

#### Acceptance Criteria (Verification Checklist)
- AC1: Container image builds successfully from source. Verify: Build log shows successful image creation. Failure: If the build fails or produces no image, the criterion fails.
- AC2: Pipeline deploys the container to the target environment. Verify: Deployment log shows successful rollout. Failure: If deployment fails or the service is not reachable after deploy, the criterion fails.
- AC3: Pipeline verifies health check after deployment. Verify: Pipeline log shows health check pass. Failure: If the health check is not executed or fails after deployment, the criterion fails.

#### Definition of Done (Hard)
- [ ] Pipeline configuration merged
- [ ] Successful deployment verified via pipeline logs
- [ ] Health check passing post-deployment

#### Dependencies
- WDD-EX-001, WDD-EX-002, and WDD-EX-003 must be complete (application code must exist to build)
- Container registry must be accessible
- Target environment must be provisioned
- Platform base image and build tooling available per ACF

#### Rollback / Failure Behavior
- If deployment fails health check, automatic rollback to previous container image
- If rollback also fails, manual intervention required; incident raised

---

### WDD Item
- WDD Item ID: WDD-EX-005
- Parent TDD Section: Section 8 (Testing Strategy)
- Assignee Type: Either
- Required Capabilities: backend, testing
- Complexity Estimate: M — Multiple test types (unit + integration + failure) across multiple components with 4 acceptance criteria; requires running service for integration tests.

#### Intent (1–2 sentences)
Implement unit tests and integration tests for the Reference Data Service as defined in the TDD testing strategy.

#### In Scope
- Unit tests for data access component
- Unit tests for authentication validation
- Integration tests for success, not-found, and unauthorized scenarios
- Failure test for data store unavailability
- Test report generation

#### Out of Scope / Non-Goals
- Performance or load testing
- UI testing
- End-to-end tests across multiple services

#### Inputs
- Deployed API endpoint (WDD-EX-001, WDD-EX-002, and WDD-EX-003 must be complete)
- TDD-EX-001 testing strategy (Section 8)
- Test framework provided by platform

#### Outputs
- Passing test suite with report artifact covering unit and integration tests

#### Acceptance Criteria (Executable)
- AC1: Given the test suite, when unit tests are run, then all data access and auth validation tests pass. Failure: If any unit test fails or a required test case is missing (data access CRUD, auth valid/invalid), the criterion fails.
- AC2: Given a running service, when integration tests are run, then success (200), not-found (404), and unauthorized (401) scenarios pass. Failure: If any integration test fails or a required scenario is not covered, the criterion fails.
- AC3: Given a simulated data store failure, when the failure test is run, then the service returns 500 and does not crash. Failure: If the service crashes or returns an unexpected status, the criterion fails.
- AC4: Given all tests pass, when the test report is generated, then it is accessible as a build artifact. Failure: If the report is not generated or is not accessible, the criterion fails.

#### Definition of Done (Hard)
- [ ] PR merged
- [ ] All unit tests passing
- [ ] All integration tests passing
- [ ] Test report generated and accessible as build artifact

#### Dependencies
- WDD-EX-001, WDD-EX-002, and WDD-EX-003 must be complete (API must be functional for integration tests)

#### Rollback / Failure Behavior
- If tests fail, the build is blocked; no deployment occurs
- Test failures do not affect the running service

---

## 3. Work Groups

### Work Group
- Group ID: WG-1
- Group Name: Core API Functionality
- Business Capability: Reference data is retrievable via authenticated API with health reporting and structured logging
- Member Items: WDD-EX-001, WDD-EX-002, WDD-EX-003
- Acceptance Criteria (Group-Level):
  - Given the API code is complete, when an authenticated consumer requests reference data by key, then the correct data is returned, the health endpoint reports status, and structured logs are emitted for all requests

### Work Group
- Group ID: WG-2
- Group Name: Deployment and Verification
- Business Capability: Service is deployed to production runtime with verified test coverage
- Member Items: WDD-EX-004, WDD-EX-005
- Acceptance Criteria (Group-Level):
  - Given WG-1 is complete, when the service is deployed via the pipeline and all tests are run, then the deployment succeeds with health check passing and all unit and integration tests produce a passing report

---

## 4. Mandatory Granularity Rules (Hard)

Every WDD item must satisfy all of the following:

- One observable outcome
- One pull request
- One subsystem or repo
- No design decisions
- No cross-environment deployment
- Completable in a short, bounded time window

Items violating these rules must be split.

---

## 5. Freeze Declaration
This WDD is approved and frozen. Execution may proceed.

- Approved By: Example Team
- Date: 2025-01-28
