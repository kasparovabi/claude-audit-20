# Pass 15 - Dependency & supply chain review

From manifests and lockfiles: flag dependencies that are unused (declared but never imported), duplicated across the tree in conflicting versions, abandoned or archived upstream, or pulled in for one trivial function. Check for install-time script execution and typosquat-suspicious names. Identify which dependencies sit on security-critical paths (auth, crypto, parsing untrusted input); these deserve version pinning and priority updates. You cannot query CVE feeds live: mark version-vulnerability claims NEEDS-CONTEXT with the exact package@version to check.

## Output contract

One block per finding:

```
[P15] <SEVERITY> <manifest/lockfile>:<line or package>
Claim: one-sentence concrete defect
Evidence: what shows it (import search result, version tree, upstream status)
Failure: the concrete risk this creates
Fix: the action, named (remove / dedupe / pin / replace / audit)
```

SEVERITY: CRITICAL / HIGH / MEDIUM / LOW / NIT. Version-vulnerability claims: always `NEEDS-CONTEXT` with the exact `package@version` to check against CVE feeds.

Then: the list of dependencies on security-critical paths (auth, crypto, untrusted-input parsing).

End with one line: `P15: CLEAN | N-A (<reason>) | FINDINGS(<n>)`
