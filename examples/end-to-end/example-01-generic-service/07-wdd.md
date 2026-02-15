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
- WDD Item ID: WDD-1
- Parent TDD Section: Sections 4, 5, 6, 7 (Interfaces, Build/Deploy, Failure Handling, Observability)
- Assignee Type: Either

#### Intent (1-2 sentences)
Implement the read-only API endpoint, data access layer, authentication validation, health check, and structured logging. Deploy via the standard pipeline.

#### In Scope
- GET /reference-data/{key} endpoint with all response codes (200, 401, 404, 500)
- GET /health endpoint
- Data access component reading from relational data store
- Service-to-service authentication validation
- Structured JSON request logging
- Container build and pipeline deployment configuration

#### Out of Scope / Non-Goals
- Write endpoints
- UI components
- External network configuration
- Test implementation (covered by WDD-2)

#### Inputs
- TDD-EX-001 interface contracts (Section 4)
- Data store connection string (via secrets manager)
- Platform identity provider configuration
- Container registry credentials (via pipeline)

#### Outputs
- Deployed, functional API endpoint serving reference data
- Health check endpoint operational
- Structured logs flowing to aggregator
- Container image in registry

#### Acceptance Criteria (Executable)
- AC1: Given a valid key and valid credentials, when GET /reference-data/{key} is called, then the correct data is returned with status 200
- AC2: Given an invalid key and valid credentials, when GET /reference-data/{key} is called, then a 404 error is returned
- AC3: Given missing or invalid credentials, when any endpoint is called, then a 401 error is returned
- AC4: Given the data store is unavailable, when GET /reference-data/{key} is called, then a 500 error is returned and the service does not crash
- AC5: Given the service is deployed, when GET /health is called, then status 200 is returned

#### Definition of Done (Hard)
- [ ] PR merged
- [ ] Unit tests passing
- [ ] Health check verified
- [ ] Structured logs confirmed in aggregator

#### Dependencies
- Data store must be provisioned and accessible
- Platform identity provider must be configured

#### Rollback / Failure Behavior
- If deployment fails health check, automatic rollback to previous container image
- If data store is unavailable at runtime, API returns 500; service remains running

---

### WDD Item
- WDD Item ID: WDD-2
- Parent TDD Section: Section 8 (Testing Strategy)
- Assignee Type: Either

#### Intent (1-2 sentences)
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
- Deployed API endpoint (WDD-1 must be complete)
- TDD-EX-001 testing strategy (Section 8)
- Test framework provided by platform

#### Outputs
- Passing test suite
- Test report artifact

#### Acceptance Criteria (Executable)
- AC1: Given the test suite, when unit tests are run, then all data access and auth tests pass
- AC2: Given a running service, when integration tests are run, then success, not-found, and unauthorized scenarios pass
- AC3: Given a simulated data store failure, when the failure test is run, then the service returns 500 and does not crash
- AC4: Given all tests pass, when the test report is generated, then it is accessible as a build artifact

#### Definition of Done (Hard)
- [ ] PR merged
- [ ] All tests passing
- [ ] Test report generated and accessible

#### Dependencies
- WDD-1 must be complete (API must be deployed and functional)

#### Rollback / Failure Behavior
- If tests fail, the build is blocked; no deployment occurs
- Test failures do not affect the running service

---

## 3. Mandatory Granularity Rules (Hard)

Every WDD item must satisfy all of the following:

- One observable outcome
- One pull request
- One subsystem or repo
- No design decisions
- No cross-environment deployment
- Completable in a short, bounded time window

Items violating these rules must be split.

---

## 4. Freeze Declaration
This WDD is approved and frozen. Execution may proceed.

- Approved By: Example Team
- Date: 2025-01-28
