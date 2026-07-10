# Pass 10 - Memory & unbounded growth

Hunt for state that only grows: caches without eviction or TTL; collections appended to per-request at module/global scope; unbounded queues/buffers; loading entire datasets or files into memory to process a fraction; large object graphs retained by closures, listeners, or static references; per-user/per-session state never cleaned up. For each, state the growth driver (per request? per user? per day?) and the eventual failure mode.

## Output contract

One block per finding:

```
[P10] <SEVERITY> <file>:<line>
Claim: one-sentence concrete defect
Evidence: the code that shows it
Failure: growth driver (per request / per user / per day) and the eventual failure mode
Fix: the missing defense, named
```

SEVERITY: CRITICAL / HIGH / MEDIUM / LOW / NIT. If a finding depends on unseen code or config, tag it `NEEDS-CONTEXT` and name exactly what is missing.

End with one line: `P10: CLEAN | N-A (<reason>) | FINDINGS(<n>)`
