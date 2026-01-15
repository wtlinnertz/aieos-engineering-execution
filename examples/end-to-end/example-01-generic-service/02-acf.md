# ACF — Standard Service Guardrails

## Platform Assumptions
- Managed container runtime
- Standard CI/CD pipeline

## Security Guardrails
- Service-to-service authentication required
- No public ingress

## Reliability Guardrails
- Rollback must be supported
- Failures must be observable

## Forbidden Patterns
- Direct database access by consumers
