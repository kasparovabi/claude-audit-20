# Pass 09 - Algorithmic complexity & hot paths

Identify the paths most frequently executed or handling the largest data (request handlers, loops over collections, serialization). For each, state the load model you assume ("N items per request, M concurrent"), then flag: nested iteration over unbounded data, linear scans inside loops (accidental O(n²)), repeated recomputation of invariants, redundant serialization/parsing round-trips. Every finding must state the scale at which it starts to hurt; patterns that never see scale are NITs.

## Output contract

One block per finding:

```
[P09] <SEVERITY> <file>:<line>
Claim: one-sentence concrete defect, with the assumed load model
Evidence: the code that shows it
Failure: the scale at which it starts to hurt, and what the user/system experiences
Fix: the missing defense, named
```

SEVERITY: CRITICAL / HIGH / MEDIUM / LOW / NIT. Patterns that never see scale are NITs. If a finding depends on unseen code or config, tag it `NEEDS-CONTEXT` and name exactly what is missing.

End with one line: `P09: CLEAN | N-A (<reason>) | FINDINGS(<n>)`
