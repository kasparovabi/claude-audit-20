# Pass 07 - Resource lifecycle & leaks

Audit acquisition and release of every scarce resource: DB connections, file handles, sockets, subprocesses, timers/intervals, event listeners, temp files, locks. For each: is release guaranteed on ALL paths including exceptions and early returns (finally/defer/RAII/using equivalents)? Are pools bounded and returns guaranteed? Are listeners/timers removed when their owner is destroyed? Flag every acquisition whose release you cannot prove from the shown code.

## Output contract

One block per finding:

```
[P07] <SEVERITY> <file>:<line>
Claim: one-sentence concrete defect (which acquisition, why release is not guaranteed)
Evidence: the code path (exception / early return) that skips release
Failure: the concrete bad outcome, and who observes it
Fix: the missing defense, named
```

SEVERITY: CRITICAL / HIGH / MEDIUM / LOW / NIT. If a finding depends on unseen code or config, tag it `NEEDS-CONTEXT` and name exactly what is missing.

End with one line: `P07: CLEAN | N-A (<reason>) | FINDINGS(<n>)`
