# Pass 05 - Error handling & failure paths

For every multi-step operation, ask: what happens when step N fails after steps 1..N-1 succeeded? Hunt for: empty or swallow-all catch blocks; errors logged and then execution continuing in a corrupt state; partial writes with no rollback or compensation; error types collapsed so callers cannot distinguish retryable from fatal; failure paths that leak resources or hold locks. Report the concrete corrupt state each gap can produce.

## Output contract

One block per finding:

```
[P05] <SEVERITY> <file>:<line>
Claim: one-sentence concrete defect
Evidence: the code or trace that shows it
Failure: the concrete corrupt state this gap produces
Fix: the missing defense, named
```

SEVERITY: CRITICAL / HIGH / MEDIUM / LOW / NIT. If a finding depends on unseen code or config, tag it `NEEDS-CONTEXT` and name exactly what is missing.

End with one line: `P05: CLEAN | N-A (<reason>) | FINDINGS(<n>)`
