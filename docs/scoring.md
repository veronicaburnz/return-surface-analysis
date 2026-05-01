# Return Surface Risk Scoring

This rubric is for triage. It is not CVSS and should not be presented as CVSS.

Use it to decide whether a return-surface differential is likely a vulnerability, a hardening recommendation, or a non-finding.

## Score each dimension from 0 to 3

| Dimension | 0 | 1 | 2 | 3 |
|---|---|---|---|---|
| Return validation gap | Return output is fully validated | Partially validated | Format-checked only | No meaningful validation |
| Boundary sensitivity | Same trust zone | Low-sensitivity boundary | Privileged service, tenant, or process boundary | Auth, code, policy, model-instruction, or cross-user boundary |
| Exposure | Not exposed | Internal-only | Authenticated external user | Public, cross-user, or broad fan-out |
| Interpretation | Discarded or inert | Stored as opaque data | Parsed, rendered, cached, or logged | Executed, trusted as authority, used for auth/policy, or injected into model/tool control flow |
| Attacker influence | No influence | Indirect or weak influence | Constrained influence | Direct or chosen influence |
| Persistence | Ephemeral | Short-lived request state | Stored in logs/cache/database | Promoted into durable artifact, memory, config, policy, or build output |

## Suggested triage

| Total | Suggested triage |
|---:|---|
| 0-4 | Usually non-finding or documentation note |
| 5-8 | Hardening recommendation or candidate risk |
| 9-13 | Likely valid security finding if reachable |
| 14-18 | High-priority finding candidate |

The score is not a replacement for evidence. It is a forcing function for consistent review.

## Required evidence for a reportable finding

A strong report should establish:

```text
attacker influence + boundary crossing + insufficient return controls + sensitive sink + concrete impact
```

If the chain is incomplete, use careful language:

```text
The evidence supports a return-surface hardening recommendation, not a confirmed vulnerability.
```

```text
The return path appears under-validated, but impact depends on whether the returned value reaches a sensitive sink.
```

```text
The trust differential is real, but I have not proven attacker control over the returned value.
```

## Example: web proxy

| Dimension | Score | Reason |
|---|---:|---|
| Return validation gap | 3 | Upstream body and headers are copied without validation |
| Boundary sensitivity | 2 | Server-side fetch crosses network boundary |
| Exposure | 3 | Returned content reaches external users |
| Interpretation | 2 | Browser renders returned response |
| Attacker influence | 2 | Attacker can influence selected upstream response within constraints |
| Persistence | 1 | Response is request-scoped, not durable |

Total: `13`, likely valid if reachable and impact is concrete.

## Example: logged-only internal error

| Dimension | Score | Reason |
|---|---:|---|
| Return validation gap | 2 | Error is not normalized |
| Boundary sensitivity | 1 | Internal service boundary |
| Exposure | 0 | Only restricted logs receive it |
| Interpretation | 1 | Log processor parses text |
| Attacker influence | 1 | Weak indirect influence |
| Persistence | 2 | Logs persist |

Total: `7`, usually hardening or observability risk unless secrets or cross-tenant exposure are proven.
