# Example: LLM Tool Output Return Surface

## Scenario

An LLM agent calls a tool with schema-validated arguments. The tool returns text that is inserted back into the model context.

```text
model plan -> validated tool arguments -> tool result -> model context
```

## Forward path controls

The system may validate:

- tool name,
- argument schema,
- argument types,
- permissions,
- user confirmation,
- rate limits.

## Return surface

The tool result may include:

- retrieved web text,
- document contents,
- API response text,
- error messages,
- hidden instructions embedded in untrusted content,
- links,
- code blocks,
- file paths,
- metadata.

## Trust differential

Tool invocation is constrained, but the returned text may become instruction-adjacent once it is inserted into model context.

## Candidate impact

Depending on model and tool design, this can produce:

- prompt injection,
- indirect tool-call steering,
- memory pollution,
- data exfiltration attempts,
- unsafe action suggestions,
- policy or role confusion.

## Review questions

```text
[ ] Is tool output clearly marked as untrusted data?
[ ] Can tool output issue instructions to the model?
[ ] Can tool output trigger later tool calls?
[ ] Can tool output enter long-term memory?
[ ] Is output length bounded?
[ ] Is provenance preserved?
[ ] Are sensitive actions gated by user confirmation?
```

## Safer design

- Keep tool output in a data-only channel where possible.
- Preserve provenance.
- Quote, fence, or summarize untrusted output.
- Use constrained schemas for tool results.
- Avoid placing raw tool output in system or developer instruction scope.
- Require user confirmation for sensitive actions.
- Treat retrieved text as evidence, not authority.
