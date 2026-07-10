# Pass 06 - Concurrency & race conditions

Within this runtime's concurrency model, hunt for: shared mutable state accessed without synchronization; check-then-act races (exists-then-create, read-then-update, balance-then-deduct); non-atomic read-modify-write on shared stores; missing awaits / fire-and-forget with side effects; lazy initialization unsafe under concurrent first access; lock ordering that can deadlock. For each finding, describe the interleaving that triggers it in one or two sentences. If the runtime makes a category impossible, say so and move on.

## Output contract

One block per finding:

```
[P06] <SEVERITY> <file>:<line>
Claim: one-sentence concrete defect
Evidence: the code that shows it
Failure: the interleaving that triggers it (one or two sentences) and the resulting bad outcome
Fix: the missing defense, named
```

SEVERITY: CRITICAL / HIGH / MEDIUM / LOW / NIT. If a finding depends on unseen code or config, tag it `NEEDS-CONTEXT` and name exactly what is missing.

For each hunted category the runtime makes impossible, say so in one line and move on.

End with one line: `P06: CLEAN | N-A (<reason>) | FINDINGS(<n>)`
