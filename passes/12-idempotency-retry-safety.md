# Pass 12 - Idempotency & retry safety

Assume every operation can be delivered twice (client retry, queue redelivery, double click, network replay). For each state-changing operation: what happens on duplicate execution? Hunt for: payments/emails/notifications sent twice; counters double-incremented; duplicate rows on retried creates; non-idempotent handlers consumed from at-least-once queues; missing idempotency keys on unsafe endpoints that clients retry. Classify each operation: naturally idempotent / protected by key or constraint / UNSAFE.

## Output contract

First the classification: EVERY state-changing operation, each tagged `naturally idempotent`, `protected by key or constraint` (name the key/constraint), or `UNSAFE`.

Then one block per finding (every UNSAFE gets one):

```
[P12] <SEVERITY> <file>:<line>
Claim: one-sentence concrete defect
Evidence: the code that shows it
Failure: what duplicate execution concretely produces
Fix: the missing defense, named
```

SEVERITY: CRITICAL / HIGH / MEDIUM / LOW / NIT. If a finding depends on unseen code or config, tag it `NEEDS-CONTEXT` and name exactly what is missing.

End with one line: `P12: CLEAN | N-A (<reason>) | FINDINGS(<n>)`
