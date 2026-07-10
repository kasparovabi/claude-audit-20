# Pass 01 - Injection & untrusted input sweep

Trace every point where external data (user input, HTTP params, headers, files, queue messages, third-party API responses, environment values) enters the system. Follow each value to its sinks: queries, shell/exec, file paths, templates, HTML output, redirects, deserializers, eval-like constructs. Flag every path where data reaches a sink without validation or context-appropriate encoding, and name the missing defense. Report per entry point, including entry points that are clean.

## Output contract

One block per finding:

```
[P01] <SEVERITY> <file>:<line>
Claim: one-sentence concrete defect
Evidence: the code or trace that shows it
Failure: the concrete bad outcome, and who observes it
Fix: the missing defense, named
```

SEVERITY: CRITICAL / HIGH / MEDIUM / LOW / NIT. If a finding depends on unseen code or config, tag it `NEEDS-CONTEXT` and name exactly what is missing.

Then the per-entry-point report: list EVERY entry point inspected with its verdict (clean entry points included).

End with one line: `P01: CLEAN | N-A (<reason>) | FINDINGS(<n>)`
