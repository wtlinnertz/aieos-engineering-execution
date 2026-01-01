# ACF Template (Architecture Context File)

The ACF defines **architecture guardrails** that constrain the SAD and downstream artifacts.
It is reusable across many initiatives.

## 0. Document Control
- ACF ID:
- Owner:
- Date:
- Status: Draft | Approved | Frozen
- Applies To: (e.g., “All services”, “All platform workloads”, “This domain only”)

## 1. Purpose
What guardrails does this ACF exist to enforce?

## 2. Platform Assumptions (High Level)
- Runtime environment(s):
- Deployment model(s):
- Networking posture (high level):
- Identity model (high level):

## 3. Security Guardrails (Hard)
Examples (customize to your organization’s baseline):
- Authentication requirements:
- Authorization expectations:
- Data classification and handling:
- Encryption requirements (in transit / at rest):
- Secrets handling expectations:

## 4. Compliance / Regulatory Constraints (Hard)
- Constraint 1:
- Constraint 2:

## 5. Reliability & Resilience Guardrails (Hard)
- Availability expectations (qualitative):
- Failure isolation expectations:
- Rollback expectations:

## 6. Observability Guardrails (Hard)
- Required telemetry types: logs, metrics, traces (as applicable)
- Minimum operational signals required:

## 7. Approved Architectural Patterns
List “allowed” patterns that are preferred or standard.
- Pattern 1:
- Pattern 2:

## 8. Forbidden Patterns (Hard)
List explicitly prohibited approaches.
- Forbidden 1:
- Forbidden 2:

## 9. Standard Interfaces / Integrations (Optional)
- Change management expectations:
- CI/CD expectations:
- Artifact storage expectations:
- Audit expectations:

## 10. Open Items
- Open item 1:
- Open item 2:

## 11. Freeze Declaration (when ready)
This ACF is approved and frozen. SAD and downstream artifacts must comply.

- Approved By:
- Date:
