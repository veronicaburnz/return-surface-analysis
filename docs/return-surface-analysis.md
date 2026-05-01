# Return Surface Analysis

Return Surface Analysis is a defensive security review heuristic for examining the path by which data, metadata, errors, artifacts, or tool output comes back from a boundary-crossing operation.

The core question is simple:

```text
What comes back, who receives it, and what does the system do with it?
```

The method is designed for systems where the forward path is obvious and specified, but the return path is implicit and assumed.

## Why this exists

Security reviews often focus on the designed direction of a system:

- request input,
- API parameters,
- outbound hostnames,
- tool-call arguments,
- event payloads,
- dependency versions,
- authentication request parameters.

That is rational because those are usually the things documented in the interface. Unfortunately, systems also have a path back:

- response bodies,
- response headers,
- error messages,
- generated artifacts,
- tool outputs,
- handler return values,
- refreshed tokens,
- cache entries,
- model context,
- parser output.

Return Surface Analysis treats this path back as a first-class review surface.

## Heuristic

When a system validates the forward path more rigorously than the return path, the less-validated return path should be treated as a distinct attack surface.

This is not a theorem. It is a review heuristic. Its value is in forcing an explicit review of a direction that is often skipped because the system already appears to have been analyzed.

## Terms

### Forward path

The explicit path from the caller, user, job, model, service, or process into a boundary-crossing action.

Examples:

- a user-supplied URL being checked before a server-side fetch,
- a tool-call argument being validated against a schema,
- an event payload being validated before publication,
- a token request being constructed with expected parameters.

### Return path

The path by which the result of that action comes back and is reused, exposed, interpreted, cached, trusted, or forwarded.

Examples:

- upstream response body copied to a client,
- upstream headers copied to a response,
- tool output inserted into model context,
- build artifact promoted to deployment,
- handler return value sent to subscribers,
- token response accepted as authority.

### Trust differential

The difference between controls applied to the forward path and controls applied to the return path.

A high trust differential exists when the forward path has explicit validation, sanitization, access control, logging, or schema enforcement, while the return path has weak or absent controls.

### Return surface

The set of return values, metadata, errors, side effects, and state transitions that can cross back from a boundary-crossing operation into a consumer.

## Procedure

### 0. Identify the intermediary

Look for an operation shaped like this:

```text
input -> action(derived_from_input) -> process(result) -> output
```

The `action` crosses a boundary. Boundaries include:

- network,
- process,
- tenant,
- privilege level,
- trust zone,
- model context,
- filesystem,
- cache,
- parser,
- renderer,
- build stage,
- authentication authority.

### 1. Inventory returned values

Do not stop at the response body. Return paths include every value that comes back or is produced after the boundary-crossing action.

Check for:

- body,
- headers,
- status code,
- error object,
- stack trace,
- redirect target,
- content type,
- cache metadata,
- token claims,
- generated file,
- artifact hash,
- tool output,
- model-visible text,
- handler return value,
- retry state,
- timing signal,
- log entry,
- database write,
- config mutation.

### 2. Measure the trust differential

Compare how the forward path is handled against how the return path is handled.

| Dimension | Forward path | Return path | Review question |
|---|---|---|---|
| Validation | Is input schema-checked, type-checked, or allowlisted? | Is returned data validated? | Is the result constrained before use? |
| Sanitization | Is input escaped, encoded, normalized, or filtered? | Is output escaped, encoded, normalized, or filtered? | Is sanitization contextual to the sink? |
| Access control | Are auth, scope, rate limit, or tenancy checks applied? | Is result exposure gated? | Can returned data reach a broader or higher-trust audience? |
| Logging | Is input audited or measured? | Is the return path logged? | Would defenders see abuse on the return side? |
| Interpretation | Is input treated as data? | Is returned content parsed, rendered, cached, trusted, or executed? | Does the return path upgrade data into authority? |
| State | Is input prevented from changing shared state directly? | Can the result alter shared state? | Can returned data pollute cache, config, memory, build output, or policy? |

### 3. Trace the high-differential direction

For each returned value, ask:

| Check | Question |
|---|---|
| Proxying | Is the result forwarded to the original caller or another user? |
| Metadata copying | Are headers, status codes, claims, labels, or other metadata copied? |
| Trust assumption | Does the code treat the result as if it came from a trusted source? |
| Zone crossing | Does returned data cross from lower-trust to higher-trust context? |
| Error leakage | Do errors reveal secrets, topology, stack traces, paths, or internal state? |
| State pollution | Can the result affect cache, config, database state, model context, environment, or build output? |
| Interpretation | Is the result parsed, rendered, compiled, executed, deserialized, or injected into another interpreter? |
| Persistence | Can the result outlive the request through logs, caches, memory, artifacts, or stored state? |
| Fan-out | Can one returned result reach many users, systems, builds, subscribers, or model turns? |

### 4. Identify sinks

A sink is where the returned value becomes meaningful.

Common return-path sinks:

- browser rendering,
- response header emission,
- JSON parsing,
- XML parsing,
- Markdown rendering,
- shell invocation,
- SQL query construction,
- template rendering,
- deserialization,
- cache key/value storage,
- log ingestion,
- model context insertion,
- tool-call planning,
- policy evaluation,
- token/session creation,
- build promotion,
- artifact execution,
- configuration update.

The same returned value can be harmless in one sink and dangerous in another. Do not assess returned data abstractly. Assess it at the sink.

### 5. Prove or downgrade

A trust differential is not automatically a vulnerability.

A strong report usually needs:

```text
attacker influence + boundary crossing + insufficient return controls + sensitive sink + concrete impact
```

If one part is missing, downgrade the finding:

- call it a hardening recommendation,
- call it a candidate risk,
- call it a design concern,
- or reject it as a non-finding.

### 6. Remediate

The general remediation is to treat returned data as untrusted input at the next boundary.

Common controls:

- output schema validation,
- response header allowlisting,
- metadata stripping,
- content-type pinning,
- contextual encoding,
- size and timeout limits,
- cache partitioning,
- error normalization,
- provenance tagging,
- artifact integrity verification,
- claim validation,
- model-context isolation,
- audit logging on returned data.

See [`remediation-patterns.md`](remediation-patterns.md) for details.

## Output labels

Use these labels when applying the method:

| Label | Meaning |
|---|---|
| `PROVEN` | A reachable attacker-influenced return path crosses a trust boundary and reaches a sensitive sink with concrete impact. |
| `RISK` | The return path appears under-specified or under-validated, but exploitability or impact is incomplete. |
| `REJECT` | The path is sufficiently constrained, lacks attacker influence, lacks a meaningful boundary, lacks exposure, or lacks a sensitive sink. |

## Compact checklist

```text
[ ] What crosses the boundary?
[ ] What comes back?
[ ] Who receives it?
[ ] Is it attacker-influenced?
[ ] Is it validated before use?
[ ] Is metadata copied?
[ ] Is it rendered, parsed, cached, trusted, executed, or persisted?
[ ] Can one user's returned data affect another user?
[ ] Can errors leak sensitive state?
[ ] Is the sink context-specific?
[ ] What concrete impact follows?
[ ] Is this a proven finding, risk, or non-finding?
```
