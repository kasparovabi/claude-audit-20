# Pass 08 - Data access patterns & N+1

Audit how the code talks to its data stores. Hunt for: queries inside loops (N+1); missing batching where the API supports it; unbounded queries (no limit/pagination) on tables that grow; SELECT-everything where few fields are used; filtering/sorting/joining in application memory that the store should do; repeated identical reads within one request with no caching; write patterns causing lock contention. Where schema/indexes are not visible, tag index-related findings NEEDS-CONTEXT and name what you need.

## Output contract

One block per finding:

```
[P08] <SEVERITY> <file>:<line>
Claim: one-sentence concrete defect
Evidence: the code or query pattern that shows it
Failure: the concrete bad outcome at realistic data volume
Fix: the missing defense, named
```

SEVERITY: CRITICAL / HIGH / MEDIUM / LOW / NIT. Index-related findings without visible schema: tag `NEEDS-CONTEXT` and name the schema/index facts you need.

End with one line: `P08: CLEAN | N-A (<reason>) | FINDINGS(<n>)`
