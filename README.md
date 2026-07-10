# audit-20

A twenty-pass verification gate for Claude Code. It runs after coding work and refuses to call the work done until every pass has executed and every confirmed finding is fixed.

The idea is simple: a code change is not finished when the code compiles. It is finished when someone has looked for the twenty ways it tends to break, one category at a time, and either found nothing or fixed what they found. This skill makes Claude do that pass automatically, and gates on 20/20.

## Why it is built this way

The twenty passes come from [a prompt Ersin Koç posted on X](https://x.com/ersinkoc/status/2074552091826413695). Each pass hunts one category: injection, auth, IDOR, secrets, error handling, races, resource leaks, N+1, hot-path complexity, unbounded memory, external-call resilience, idempotency, transaction consistency, config, supply chain, observability, API contracts, cross-module contracts, and test gaps.

Pass 20 is the load-bearing one. It runs last and does nothing but try to falsify the findings from passes 1 through 19: relocate every citation, look for a guard or type constraint that already prevents the problem, merge duplicates, and re-score severity conservatively. Findings that survive go in CONFIRMED. This is what lets the first nineteen passes stay aggressive without drowning you in false positives, since a separate skeptic filters them.

Turning the prompt into a skill set adds three things on top of the raw text:

- **A standard finding format** so pass 20 can consume the output of the other passes mechanically instead of parsing prose.
- **A fan-out harness.** For anything substantial, one subagent runs each pass in parallel, then a barrier hands all findings to pass 20 at once.
- **A gate with real teeth.** 20/20 means all twenty passes executed (a pass that does not apply finishes as N-A with a reason, which still counts as executed) and zero confirmed findings remain unfixed. Only an explicit, recorded user waiver can close a confirmed finding without a code fix.

## Install

Clone into your Claude Code skills directory:

```bash
git clone https://github.com/kasparovabi/claude-audit-20.git ~/.claude/skills/audit-20
```

The folder name matters. Claude discovers the skill by the `name:` field in `SKILL.md` (`audit-20`), and the fleet prompts point at `~/.claude/skills/audit-20/passes/`, so keep the directory named `audit-20`.

## Use

Once installed, the skill triggers on its own when coding work is wrapping up: after a feature, a bug fix, a refactor, or before a commit, PR, or deploy. You can also call it directly:

```
/audit-20
```

or just say "audit this", "verify the change", "denetle", or "20/20".

Default scope is the change plus its blast radius (the diff, plus what calls into and out of the changed code). Say "full" to sweep the whole codebase.

By default it runs in fleet mode, one subagent per pass in parallel, which finishes fast even on large diffs. Small single-module diffs run inline in one context.

## What you get back

Every run ends with a fixed report skeleton:

```
# audit-20 report - <scope>, <date>

## Scoreboard
| # | Pass | Verdict |   (CLEAN / N-A / FIXED(n) / WAIVED(n) / BLOCKED(n))

## Confirmed and fixed
## Unverified   (findings that need context you could not see)
## Rejected     (false positives pass 20 threw out, with reasons)

## Gate
PASSED 20/20  |  BLOCKED (what remains)
```

## The twenty passes

| # | Pass | Hunts for |
|---|------|-----------|
| 01 | Injection & untrusted input | untrusted data reaching sinks unvalidated or unencoded |
| 02 | Authentication & session | identity establishment and maintenance flaws |
| 03 | Authorization & IDOR | missing server-side ownership or role checks |
| 04 | Secrets & sensitive data | hardcoded secrets, PII in logs, leaky errors and responses |
| 05 | Error handling & failure paths | swallowed errors, partial writes, corrupt-state continuation |
| 06 | Concurrency & races | races, check-then-act, missing awaits, deadlocks |
| 07 | Resource lifecycle & leaks | unreleased connections, handles, timers, listeners, locks |
| 08 | Data access & N+1 | N+1, unbounded queries, in-app filtering, lock contention |
| 09 | Complexity & hot paths | accidental O(n²), recomputation, redundant round-trips |
| 10 | Memory & unbounded growth | evictionless caches, growing globals, retained graphs |
| 11 | External calls & resilience | missing timeouts, unbounded retries, undefined failure behavior |
| 12 | Idempotency & retry safety | duplicate effects under retry or redelivery |
| 13 | Transactions & consistency | multi-write operations with no transaction, saga, or outbox |
| 14 | Config & environment | dangerous defaults, env divergence, missing fail-fast |
| 15 | Dependency & supply chain | unused, duplicated, or abandoned deps, install scripts, typosquats |
| 16 | Logging & observability | diagnostic blind spots, missing audit trail, log noise |
| 17 | API contract consistency | inconsistent naming, errors, status codes, pagination |
| 18 | Cross-module contracts | responsibility gaps and double effects across boundaries |
| 19 | Test gap & assertion quality | missing failing-path tests, assertion-free tests |
| 20 | Verification & false-positive filter | false positives in passes 1-19, produces the final verdict |

## Layout

```
audit-20/
├── SKILL.md              orchestration, scope rules, the gate, the report format
└── passes/
    ├── 01-injection-untrusted-input.md
    ├── ...                each pass is self-contained: a subagent runs it with
    └── 20-verification-filter.md   only its own file plus the code under review
```

## Credit

The twenty-pass audit prompt is by **Ersin Koç ([@ersinkoc](https://x.com/ersinkoc))**: [original post](https://x.com/ersinkoc/status/2074552091826413695). This repository packages that prompt as an installable Claude Code skill with a fan-out harness and a hard gate. Thanks to him for the prompt.

## License

[MIT](LICENSE).
