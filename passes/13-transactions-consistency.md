# Pass 13 - Transaction & consistency boundaries

Find every operation that updates more than one thing (multiple rows/tables, DB + cache, DB + external service, DB + event publish). For each: is there a transaction, saga, outbox, or compensation strategy, or does partial failure leave the system inconsistent? Specifically hunt for: cache updated before/without the DB commit; events published for changes that then roll back; cross-service writes with no reconciliation. Name the exact inconsistent state each gap produces and who observes it.

## Output contract

One block per finding:

```
[P13] <SEVERITY> <file>:<line>
Claim: one-sentence concrete defect (which multi-write operation, what strategy is missing)
Evidence: the code that shows it
Failure: the exact inconsistent state produced, and who observes it
Fix: the missing defense, named (transaction / saga / outbox / compensation)
```

SEVERITY: CRITICAL / HIGH / MEDIUM / LOW / NIT. If a finding depends on unseen code or config, tag it `NEEDS-CONTEXT` and name exactly what is missing.

End with one line: `P13: CLEAN | N-A (<reason>) | FINDINGS(<n>)`
