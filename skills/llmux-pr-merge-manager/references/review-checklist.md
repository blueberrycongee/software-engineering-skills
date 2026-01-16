# Enterprise Microservice Review Checklist

Use this checklist to judge whether a PR is safe to merge under enterprise-grade, distributed open-source microservice standards.

## Architecture and boundaries

- Preserve service boundaries and avoid tight coupling across modules/services.
- Avoid hidden cross-service dependencies; document any required contract changes.
- Maintain backward compatibility for public APIs or provide clear versioning.
- Keep changes cohesive; avoid mixing unrelated refactors with feature changes.

## Reliability and resilience

- Handle errors explicitly; return clear error messages for API callers.
- Use timeouts, retries, or circuit-breaker patterns where relevant.
- Ensure idempotency for retryable operations and background jobs.
- Avoid introducing global shared state or race-prone patterns.

## Observability

- Emit structured logs with actionable context for failures.
- Add or update metrics for key paths and error rates.
- Preserve or add tracing/telemetry hooks if applicable.
- Ensure health checks and readiness signals remain accurate.

## Security and compliance

- Validate inputs at boundaries; avoid trust of external data.
- Protect secrets; avoid logging sensitive data.
- Ensure authn/authz checks are correct for new endpoints or routes.
- Avoid privilege escalation or overly broad access patterns.

## Data and migrations

- Make schema changes backward/forward compatible when possible.
- Provide migration and rollback steps if data shape changes.
- Avoid destructive changes without clear safeguards.

## Performance and scalability

- Keep algorithmic complexity reasonable for expected workloads.
- Avoid excessive allocations or unbounded memory growth.
- Confirm rate limiting and backpressure behavior is preserved.
- Consider concurrency safety, especially around shared resources.

## Tests and CI

- Tests cover main paths and edge cases for the change.
- CI checks are green and relevant tests were executed.
- Avoid flaky or non-deterministic tests; add regression tests for bugs.

## Docs and operational readiness

- Update README, CHANGELOG, or API docs when user-facing behavior changes.
- Update config or deployment docs for new settings or flags.
- Ensure feature flags or rollout guidance exist if risk is non-trivial.
