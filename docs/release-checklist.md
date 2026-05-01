# Release Checklist

Use this before making the repository public.

## Repository metadata

```text
[ ] Replace OWNER in repository links.
[ ] Choose whether to keep "Anonymous" in CITATION.cff.
[ ] Confirm license choice.
[ ] Add repository description.
[ ] Add topics: security, threat-modeling, secure-design, output-validation, return-surface-analysis, llm-security.
```

## Safety

```text
[ ] No unresolved vendor vulnerability details.
[ ] No private bounty details.
[ ] No customer data.
[ ] No secrets, tokens, internal hostnames, or private logs.
[ ] Examples are synthetic, anonymized, or public.
```

## Content

```text
[ ] README explains the heuristic without overclaiming.
[ ] Scoring is labeled as triage, not CVSS.
[ ] False positives are documented.
[ ] Remediation patterns are included.
[ ] Responsible-use language is present.
[ ] Internal framework references are removed or defined.
[ ] Legacy names are either removed or clearly marked as aliases.
```

## GitHub settings

```text
[ ] Enable private vulnerability reporting if available.
[ ] Protect main branch if accepting contributions.
[ ] Use issue templates.
[ ] Add SECURITY.md.
[ ] Add CODE_OF_CONDUCT.md if accepting public issues/PRs.
```

## First release

```text
[ ] Tag v0.1.0.
[ ] Create a short release note.
[ ] State that the method is experimental.
[ ] Invite examples, counterexamples, and false-positive reports.
```
