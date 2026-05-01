# Remediation Patterns

Return-surface problems are fixed by treating returned data as untrusted input at the next boundary.

## Prefer explicit return contracts

Define what may come back.

Examples:

- exact JSON schema,
- fixed enum,
- fixed status mapping,
- allowed headers,
- maximum body size,
- maximum stream duration,
- allowed content types,
- allowed token claims,
- expected artifact hash or signature.

## Strip by default, allowlist by exception

For response metadata, do not copy everything.

Prefer:

```text
copy allowed headers only
drop hop-by-hop headers
drop security-sensitive headers from upstream
normalize status codes when needed
set your own Content-Type
set your own cache policy
```

## Validate before interpreting

Returned data should be validated before any of these operations:

- rendering,
- parsing,
- deserializing,
- compiling,
- executing,
- injecting into prompts,
- storing in shared cache,
- writing to database,
- forwarding to users,
- using as auth/session/policy state.

## Use contextual encoding

Escaping is sink-specific.

| Sink | Required thinking |
|---|---|
| HTML | HTML escape |
| Attribute | Attribute escape |
| JavaScript | JS-string or structured serialization |
| URL | URL encoding and scheme validation |
| Header | Header allowlist and newline rejection |
| SQL | Parameterization |
| Shell | Avoid shell; otherwise strict argument handling |
| Markdown | Renderer restrictions and link/image policy |
| LLM context | Provenance boundaries and instruction isolation |
| Logs | Redaction and structured logging |

## Bound size, time, and recursion

Return paths can be abused without elaborate exploit chains.

Apply:

- body size limits,
- stream duration limits,
- redirect limits,
- decompression limits,
- parser recursion limits,
- retry limits,
- concurrency limits,
- timeout budgets.

## Normalize errors

Errors are a return path.

Avoid leaking:

- stack traces,
- filesystem paths,
- environment variables,
- internal hostnames,
- credentials,
- dependency versions,
- database messages,
- raw upstream responses.

Return stable external errors and log detailed internal errors in restricted telemetry.

## Partition shared state

If returned data can affect cache, database, model memory, build output, config, or artifacts, isolate it.

Examples:

- partition cache by tenant and authorization context,
- avoid caching attacker-influenced upstream responses,
- separate internal and external artifact stores,
- tag provenance,
- prevent untrusted outputs from becoming trusted inputs in later stages.

## LLM-specific controls

For tool-using agents:

- validate tool outputs,
- summarize through a constrained schema,
- separate tool data from instructions,
- quote or fence untrusted tool output,
- avoid placing raw tool output in system/developer instruction scope,
- track provenance,
- restrict tool output size,
- treat retrieved text as data, not authority.

## Auth-specific controls

For auth delegation and token flows:

- validate issuer,
- validate audience,
- validate expiry and not-before,
- validate nonce and state,
- validate signature,
- validate key source,
- reject unexpected claims,
- bind returned authority to the initiating request,
- avoid trusting IdP responses only because they came from the IdP endpoint.

## Build-system controls

For build and CI/CD return paths:

- verify generated artifacts,
- sign artifacts,
- use reproducible builds where practical,
- separate build input verification from artifact verification,
- restrict artifact promotion,
- scan generated outputs,
- store provenance,
- require deploy-stage policy checks.
