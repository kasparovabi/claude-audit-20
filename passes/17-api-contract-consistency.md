# Pass 17 - API contract consistency

Audit the API surface (external or internal) as a consumer would: naming and casing consistency across endpoints; error response shape uniform and machine-parseable; status codes/error types used consistently (same failure → same code everywhere); pagination, filtering, and sorting conventions uniform; nullable vs. absent fields deliberate and consistent; breaking-change risks (fields renamed/removed, semantics changed) versus any versioning policy. Report inconsistencies in pairs: "endpoint A does X, endpoint B does Y for the same concept."

## Output contract

One block per finding, always naming BOTH sides of the inconsistency:

```
[P17] <SEVERITY> <fileA>:<line> vs <fileB>:<line>
Claim: endpoint A does X, endpoint B does Y for the same concept
Evidence: both code locations quoted
Failure: what a consumer gets wrong because of the divergence
Fix: the convention to converge on
```

SEVERITY: CRITICAL / HIGH / MEDIUM / LOW / NIT. If a finding depends on unseen code or config, tag it `NEEDS-CONTEXT` and name exactly what is missing.

End with one line: `P17: CLEAN | N-A (<reason>) | FINDINGS(<n>)`
