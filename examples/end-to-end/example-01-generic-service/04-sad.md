# SAD — Reference Data Service

## Intent Summary
(See `03-intent-summary.md`)

## Scope
- API for reading reference data
- Internal consumption only

## Non-Goals
- Data mutation
- User interface

## System Context
The system exposes a read-only API consumed by internal services.

## Major Components
- API component
- Data access component

## Architectural Decisions
- Use a simple request/response model
- Centralize data access logic

## Constraints
- Must comply with ACF guardrails

## Deferred Decisions
- Storage technology selection

## Freeze Declaration
Approved and frozen.
