# Pass 02 - Authentication & session handling

Audit only how identity is established and maintained. Check: endpoints reachable without authentication that shouldn't be; token/session generation (entropy, expiry, rotation); credential storage and comparison (hashing algorithm, constant-time checks); logout and invalidation actually revoking access; password reset and account recovery flows; remember-me and refresh-token handling. For each mechanism, state what you verified vs. what depends on framework behavior you cannot see.

## Output contract

One block per finding:

```
[P02] <SEVERITY> <file>:<line>
Claim: one-sentence concrete defect
Evidence: the code or trace that shows it
Failure: the concrete bad outcome, and who observes it
Fix: the missing defense, named
```

SEVERITY: CRITICAL / HIGH / MEDIUM / LOW / NIT. If a finding depends on unseen code or config, tag it `NEEDS-CONTEXT` and name exactly what is missing.

Then, per mechanism inspected: what you verified directly vs. what rests on framework behavior you could not see.

End with one line: `P02: CLEAN | N-A (<reason>) | FINDINGS(<n>)`
