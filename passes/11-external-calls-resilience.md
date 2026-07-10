# Pass 11 - External calls, timeouts & resilience

List every call that leaves the process (HTTP, DB, cache, queue, third-party SDKs). For each, verify: an explicit timeout exists (flag every infinite-default); failure is handled distinctly from success-with-bad-data; retries, if any, are bounded with backoff and only on idempotent operations; one slow dependency cannot exhaust the caller's threads/connections/event loop; user-facing behavior on dependency failure is defined, not accidental. Output a table: call site → timeout → retry policy → failure behavior.

## Output contract

First the table, one row per out-of-process call site (clean rows included):

| Call site (file:line) | Timeout | Retry policy | Failure behavior |
|---|---|---|---|

Then one block per finding:

```
[P11] <SEVERITY> <file>:<line>
Claim: one-sentence concrete defect
Evidence: the code that shows it
Failure: the concrete bad outcome, and who observes it
Fix: the missing defense, named
```

SEVERITY: CRITICAL / HIGH / MEDIUM / LOW / NIT. If a finding depends on unseen code or config (e.g. a client's default timeout), tag it `NEEDS-CONTEXT` and name exactly what is missing.

End with one line: `P11: CLEAN | N-A (<reason>) | FINDINGS(<n>)`
