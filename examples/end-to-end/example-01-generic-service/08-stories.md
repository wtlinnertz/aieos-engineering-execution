# Stories — Reference Data Service

## Story 1: Implement API Endpoint

### Summary
Implement the read-only Reference Data Service API with authentication, error handling, logging, and health check.

### Traceability (Hard)
- WDD Item ID: WDD-1
- Parent TDD Section: TDD-EX-001, Sections 4, 5, 6, 7

### Intent (1-2 sentences)
Implement the read-only API endpoint, data access layer, authentication validation, health check, and structured logging. Deploy via the standard pipeline.

### Scope
#### In Scope
- GET /reference-data/{key} endpoint (200, 401, 404, 500 responses)
- GET /health endpoint
- Data access component
- Authentication validation
- Structured JSON logging
- Container build and deployment

#### Out of Scope / Non-Goals
- Write endpoints
- UI components
- External network configuration
- Test implementation

### Inputs
- TDD-EX-001 interface contracts (Section 4)
- Data store connection string (via secrets manager)
- Platform identity provider configuration

### Outputs
- Deployed API endpoint
- Health check endpoint operational
- Structured logs flowing to aggregator

### Acceptance Criteria (Executable)
- Given a valid key and valid credentials, when GET /reference-data/{key} is called, then the correct data is returned with status 200
- Given an invalid key and valid credentials, when GET /reference-data/{key} is called, then a 404 error is returned
- Given missing or invalid credentials, when any endpoint is called, then a 401 error is returned
- Given the data store is unavailable, when GET /reference-data/{key} is called, then a 500 error is returned and the service does not crash
- Given the service is deployed, when GET /health is called, then status 200 is returned

### Definition of Done (Hard)
- [ ] PR merged
- [ ] Unit tests passing
- [ ] Health check verified
- [ ] Structured logs confirmed in aggregator

### Dependencies
- Data store must be provisioned and accessible
- Platform identity provider must be configured

### Rollback / Failure Behavior
- If deployment fails health check, automatic rollback to previous container image
- If data store is unavailable at runtime, API returns 500; service remains running

### Execution Ownership (Hard)
Assignee Type: Either

---

## Story 2: Add Test Coverage

### Summary
Implement unit tests and integration tests for the Reference Data Service.

### Traceability (Hard)
- WDD Item ID: WDD-2
- Parent TDD Section: TDD-EX-001, Section 8

### Intent (1-2 sentences)
Implement unit tests and integration tests as defined in the TDD testing strategy, and generate a test report artifact.

### Scope
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

### Inputs
- Deployed API endpoint (WDD-1 complete)
- TDD-EX-001 testing strategy (Section 8)

### Outputs
- Passing test suite
- Test report artifact

### Acceptance Criteria (Executable)
- Given the test suite, when unit tests are run, then all data access and auth tests pass
- Given a running service, when integration tests are run, then success, not-found, and unauthorized scenarios pass
- Given a simulated data store failure, when the failure test is run, then the service returns 500 and does not crash
- Given all tests pass, when the test report is generated, then it is accessible as a build artifact

### Definition of Done (Hard)
- [ ] PR merged
- [ ] All tests passing
- [ ] Test report generated and accessible

### Dependencies
- WDD-1 must be complete (API must be deployed and functional)

### Rollback / Failure Behavior
- If tests fail, the build is blocked; no deployment occurs
- Test failures do not affect the running service

### Execution Ownership (Hard)
Assignee Type: Either
