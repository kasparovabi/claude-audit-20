# Pass 18 - Cross-module contracts & emergent risks

Ignore single-file defects; audit interactions. For each module boundary you can see both sides of, reconstruct the implicit contract: who validates, who authorizes, who retries, who owns the transaction, which invariants each side assumes. Then hunt mismatches: responsibility gaps (each side assumes the other does it), double effects (both retry/cache/encode), invariant drift, failure-mode mismatch (one throws, the other expects codes), ordering coupling, fan-out amplification. Every finding names BOTH sides with locations. Start with a COVERAGE list: boundaries seen end-to-end vs. one side only; one-sided boundaries yield NEEDS-CONTEXT items, never conclusions.

## Output contract

FIRST the coverage list: every module boundary, tagged `end-to-end` (both sides visible) or `one-sided` (name the unseen side). One-sided boundaries produce only `NEEDS-CONTEXT` items, never conclusions.

Then one block per finding, always naming both sides:

```
[P18] <SEVERITY> <sideA file>:<line> + <sideB file>:<line>
Claim: one-sentence contract mismatch (gap / double effect / drift / failure-mode / ordering / fan-out)
Evidence: what each side assumes, quoted from both locations
Failure: the concrete bad outcome when the mismatch fires
Fix: which side takes the responsibility, named
```

SEVERITY: CRITICAL / HIGH / MEDIUM / LOW / NIT.

End with one line: `P18: CLEAN | N-A (<reason>) | FINDINGS(<n>)`
