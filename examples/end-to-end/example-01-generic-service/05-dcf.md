# DCF — Standard Delivery Design Expectations

## 0. Document Control
- DCF ID: DCF-EX-001
- Owner: Engineering Team (Example)
- Date: 2025-01-10
- Status: Frozen
- Applies To: All delivery designs for internal services

## 1. Purpose
Define the design-level standards and quality expectations that all TDDs must follow when designing internal services on the standard platform.

## 2. Design Principles (Hard)
- Prefer deterministic designs over exploratory steps
- Make interfaces explicit (inputs, outputs, error modes)
- Design for rollback and failure from the start
- Prefer the smallest safe change

## 3. Quality Bars (Hard)
- Interfaces and contracts must be explicit with request/response schemas
- Failure and rollback behavior must be defined for every component
- Tests must be defined with pass/fail criteria before implementation
- Operability must be documented (health checks, logging, metrics)

## 4. Non-Goals Enforcement (Hard)
- TDD must explicitly restate non-goals from SAD
- TDD must not implement excluded functionality
- "Helpful" scope expansion is not allowed
- Any deviation from SAD scope requires explicit re-entry to SAD stage

## 5. Operational Expectations (Hard)
- Deployment verification: Health check must pass after deployment
- Monitoring/alerting: Request rate, error rate, and latency must be observable
- Auditability: All requests must produce structured logs

## 6. Testing Expectations (Hard)
- Required test layers: Unit tests and integration tests
- Evidence requirements: Test reports must be generated and accessible
- Promotion gates: All tests must pass before deployment to any environment

## 7. Documentation Expectations (Hard)
- Required sections in a TDD: Interfaces, Build/Deploy, Failure Handling, Testing Strategy
- Required diagram types: None (kept simple for this context)
- Required traceability markers: SAD ID and ACF ID must be referenced

## 8. Open Items
- None

## 9. Freeze Declaration
This DCF is approved and frozen. All TDDs must comply.

- Approved By: Engineering Team (Example)
- Date: 2025-01-10
