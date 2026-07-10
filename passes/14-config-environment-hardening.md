# Pass 14 - Configuration & environment hardening

Audit configuration across environments: dangerous defaults (debug mode, permissive CORS, disabled TLS verification, open admin ports); prod/dev config divergence that changes security behavior; config values trusted without validation at startup; feature flags that disable security controls; identical secrets/keys across environments; missing fail-fast when required config is absent (silently running misconfigured). Report per config file/source, including which environments you could not inspect.

## Output contract

One block per finding:

```
[P14] <SEVERITY> <file>:<line>
Claim: one-sentence concrete defect
Evidence: the config value/absence that shows it
Failure: the concrete bad outcome, and in which environment
Fix: the missing defense, named
```

SEVERITY: CRITICAL / HIGH / MEDIUM / LOW / NIT. If a finding depends on unseen code or config, tag it `NEEDS-CONTEXT` and name exactly what is missing.

Then the per-source report: every config file/source inspected with its verdict, plus an explicit list of environments you could NOT inspect.

End with one line: `P14: CLEAN | N-A (<reason>) | FINDINGS(<n>)`
