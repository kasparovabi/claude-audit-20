# Pass 03 - Authorization & IDOR sweep

Assume authentication works; audit only WHO may do WHAT. For every operation that touches a resource owned by a user/tenant/org: is ownership or role verified server-side, before the action, on every path (including bulk, batch, export, and admin variants)? Flag any object ID accepted from the client and used without an ownership check (IDOR). Flag privilege checks done client-side, after the effect, or only on the happy path. List every endpoint/operation you inspected with its verdict.

## Output contract

One block per finding:

```
[P03] <SEVERITY> <file>:<line>
Claim: one-sentence concrete defect
Evidence: the code or trace that shows it
Failure: the concrete bad outcome, and who observes it
Fix: the missing defense, named
```

SEVERITY: CRITICAL / HIGH / MEDIUM / LOW / NIT. If a finding depends on unseen code or config, tag it `NEEDS-CONTEXT` and name exactly what is missing.

Then the endpoint list: EVERY endpoint/operation inspected, each with its verdict (clean ones included).

End with one line: `P03: CLEAN | N-A (<reason>) | FINDINGS(<n>)`
