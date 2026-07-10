# Pass 19 - Test gap & assertion quality

Map existing tests against risk, not coverage percentage. Identify: critical paths (auth, money, data mutation, the findings of previous passes) with no failing-path tests; tests asserting implementation details instead of behavior (break on rename, survive real bugs); tests with no meaningful assertions (run-without-error only); shared mutable fixtures causing order dependence; concurrency- and boundary-value blind spots. Output a ranked list: the ten missing tests that would catch the most damage, each with the exact scenario to assert.

When findings from passes 1-19 are available in your input, treat them as critical paths that need tests.

## Output contract

One block per finding (weak/absent test):

```
[P19] <SEVERITY> <file>:<line or "missing">
Claim: one-sentence concrete gap
Evidence: the test (or its absence) that shows it
Failure: the real bug class that would ship undetected
Fix: the test to add or repair, named
```

SEVERITY: CRITICAL / HIGH / MEDIUM / LOW / NIT. If a finding depends on unseen code or config, tag it `NEEDS-CONTEXT` and name exactly what is missing.

Then the ranked list: the ten missing tests that would catch the most damage, each with the exact scenario to assert.

End with one line: `P19: CLEAN | N-A (<reason>) | FINDINGS(<n>)`
