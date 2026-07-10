# Pass 16 - Logging, observability & auditability

Evaluate whether production failures can be diagnosed from the outside. Check: errors logged with enough context (correlation/request ID, relevant IDs but no PII/secrets; cross-check with pass 04); log levels meaningful (not everything ERROR or everything INFO); security-relevant events (login failures, permission denials, admin actions) leaving an audit trail; silent failure points with no signal at all; noisy logs in tight loops. Output: the top diagnostic blind spots, meaning incidents that would be impossible to reconstruct from the outside.

## Output contract

One block per finding:

```
[P16] <SEVERITY> <file>:<line>
Claim: one-sentence concrete defect
Evidence: the code that shows it
Failure: the incident that could not be diagnosed/reconstructed because of it
Fix: the missing defense, named
```

SEVERITY: CRITICAL / HIGH / MEDIUM / LOW / NIT. If a finding depends on unseen code or config, tag it `NEEDS-CONTEXT` and name exactly what is missing.

Then: the top diagnostic blind spots, ranked - incidents that would be impossible to reconstruct.

End with one line: `P16: CLEAN | N-A (<reason>) | FINDINGS(<n>)`
