# Pass 04 - Secrets & sensitive data exposure

Hunt for: hardcoded credentials, API keys, tokens in code/config/tests/fixtures; secrets in log statements or error messages; PII written to logs; verbose error responses leaking stack traces, queries, or internal paths; sensitive fields returned in API responses that callers don't need; debug endpoints or flags. Check configs and example/env files, not just source. Report every hit with exact location and the exposure path (who can see it).

Never print a secret's value in your own output; mask it (first 4 chars + length is enough to identify it).

## Output contract

One block per finding:

```
[P04] <SEVERITY> <file>:<line>
Claim: one-sentence concrete defect
Evidence: the code or trace that shows it (secret values MASKED)
Failure: the exposure path - who can see it
Fix: the missing defense, named
```

SEVERITY: CRITICAL / HIGH / MEDIUM / LOW / NIT. If a finding depends on unseen code or config, tag it `NEEDS-CONTEXT` and name exactly what is missing.

End with one line: `P04: CLEAN | N-A (<reason>) | FINDINGS(<n>)`
