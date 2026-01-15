# PRD — Reference Data Service

## 0. Document Control
- PRD ID: PRD-EX-001
- Author: Example Team
- Status: Frozen

## 1. Problem Statement
Multiple internal systems require access to the same reference data.
Today, each system embeds its own copy, leading to inconsistency and duplication.

## 2. Goals
- Provide a single authoritative source for reference data
- Reduce duplication across consuming systems

## 3. Non-Goals
- No UI or human-facing interface
- No write or mutation capability
- No external exposure

## 4. Users / Personas
- Internal service consumers
- Automated batch processes

## 5. Requirements

### Functional
- The system SHALL expose an API to retrieve reference data by key
- The system SHALL return data in a consistent schema

### Non-Functional
- The system SHALL respond within acceptable latency for internal calls
- The system SHALL be available during business hours

## 6. Constraints
- Must run on the standard platform runtime
- Must follow organization security baseline

## 7. Assumptions
- Reference data volume is modest
- Consumers can tolerate eventual consistency

## 8. Open Questions
- None

## 9. Success Criteria
- Consumers migrate to the new service
- Duplicate implementations are removed

## 10. Freeze Declaration
Approved and frozen.
