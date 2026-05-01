# Example: API Error Leakage Return Surface

## Scenario

An API validates request input against a schema. When a backend operation fails, the raw error object is returned to the client.

```text
validated request -> backend call -> raw error -> client response
```

## Forward path controls

The system may validate:

- request body schema,
- parameter types,
- authentication,
- authorization,
- rate limits.

## Return surface

The error may include:

- stack trace,
- filesystem path,
- internal hostname,
- dependency version,
- database error,
- query fragment,
- service topology,
- raw upstream response,
- token or secret accidentally included in exception context.

## Trust differential

Request format is contractual and validated, but error format is ad hoc and may cross to a less-trusted consumer.

## Candidate impact

Depending on content, this can produce:

- information disclosure,
- topology leakage,
- secret leakage,
- exploit development assistance,
- cross-tenant data exposure if error context includes another tenant's state.

## Review questions

```text
[ ] Are external errors normalized?
[ ] Are internal details logged only in restricted telemetry?
[ ] Are upstream error bodies copied directly?
[ ] Are stack traces suppressed externally?
[ ] Are database messages redacted?
[ ] Are error IDs used for correlation instead of raw detail exposure?
```

## Safer design

- Return stable external error codes.
- Use correlation IDs.
- Log detailed errors internally with access controls.
- Strip secrets and tokens from error contexts.
- Normalize backend errors at the boundary.
- Test failure cases, not only success cases.
