# Pass 20 - Verification & false-positive filter (always run last)

Input: all findings from previous passes plus the code. For each finding:

- Locate the citation; a mismatch means REJECTED unless you can relocate it.
- Attempt to falsify it: an existing guard, validation, type constraint, or framework behavior that already prevents the problem means REJECTED, with the disproving location cited.
- Findings that depend on unseen code remain UNVERIFIED, with the exact files needed.

Merge duplicates across passes; keep the best writeup. Re-score severity conservatively: CRITICAL only if you can articulate the concrete failure yourself. You may not add new findings.

## Output contract

Exactly three sections:

```
## CONFIRMED (sorted by severity)
[P<NN>] <SEVERITY> <file>:<line> - claim + why falsification failed

## UNVERIFIED (with required context)
[P<NN>] <file>:<line> - claim + the exact files/facts needed

## REJECTED (one-line reasons)
[P<NN>] <file>:<line> - reason, with the disproving location cited
```

End with one line: `P20: CONFIRMED(<n>) UNVERIFIED(<n>) REJECTED(<n>)`

The gate downstream of this pass: the work reaches 20/20 only when all 20 passes executed AND this CONFIRMED section is empty (after fixes and re-runs). Do not soften a finding to empty the section; findings leave CONFIRMED only through a code fix or an explicit user waiver recorded upstream.
