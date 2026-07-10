---
name: audit-20
description: Twenty-pass verification gate for any code change. Covers injection, authentication, authorization/IDOR, secrets exposure, error handling, concurrency, resource leaks, data access/N+1, algorithmic hot paths, unbounded memory growth, external-call resilience, idempotency, transaction consistency, config hardening, supply chain, observability, API contracts, cross-module contracts, test gaps, and a mandatory final false-positive filter. Use this skill EVERY time coding work is being wrapped up - after implementing a feature, fixing a bug, refactoring, generating code, before any commit/PR/deploy, and before telling the user the work is done. Also use when the user says audit, verify, review, harden, "denetle", "doğrula", "20 doğrulama", "20/20" or "audit-20". Coding work does not count as finished until it passes this gate 20/20.
---

# audit-20: the 20-pass gate

No coding deliverable is done until all 20 passes have run over it and every CONFIRMED finding is fixed. The pass definitions live in `passes/` next to this file (one file per pass, self-contained so a subagent can run a pass with only its file plus the code). Pass text is authoritative; do not soften or skip parts of it.

## 1. Establish scope

Default scope = the change plus its blast radius:

- In a git repo: uncommitted work plus the branch diff against the default branch, plus files that call into or are called by the changed code.
- No git: files modified in this session plus their direct dependents.
- User says "full", or this is the first audit of a project: the whole codebase.

Before running any pass, write a short scope brief that every pass receives: language(s), framework(s), how requests/inputs enter the system, which data stores and external services are touched, and the file list. Building this once keeps 20 passes from re-discovering the same facts.

## 2. Run passes 1-19, then 20

All 20 passes always run. A pass that does not apply finishes in seconds with verdict `N-A` plus a one-line reason (example: pass 06 concurrency on a single-threaded script with no shared state). N-A with a reason counts as executed; a skipped pass does not. Pass 20 ALWAYS runs last and receives every finding from passes 1-19.

Pick an execution mode by size:

**Inline mode** (only when the diff is under ~300 lines AND confined to a single module): run the passes yourself, in order, grouping related ones: 01-04 (security), 05-07 (failure/concurrency/resources), 08-10 (performance/memory), 11-13 (resilience/consistency), 14-15 (config/supply chain), 16-19 (observability/contracts/tests), then 20. Read each pass file before running it.

**Fleet mode** (the default; when in doubt, fleet): one subagent per pass, launched in parallel. Prompt template per agent:

```
Read ~/.claude/skills/audit-20/passes/<NN>-<name>.md and execute that pass
over this scope: <scope brief + file list>.
Follow the pass file's output contract exactly. Your final message is raw
data for a verification stage, not prose for a human.
```

When instantiating the template, substitute the expanded absolute path (the Read tool does not expand `~`). The template covers passes 1-19 only; the pass-20 agent's prompt replaces the file list with the deduped findings list plus the scope brief, since pass 20's input is findings, not files.

A barrier after passes 1-19 is correct here: pass 20 genuinely needs ALL findings at once. Before pass 20, dedup findings that cite the same file:line and defect (pass 18 findings dedup on their location pair), keeping the best writeup. Then run pass 20 as one verifier agent (or several, splitting the findings list, when there are many). When the Workflow tool is available and the task is substantive, use it for this fan-out; otherwise parallel Agent calls work.

Model choice: mechanical sweeps (04, 14, 15, 16) tolerate a smaller model; passes 01-03, 06, 13, 18 and 20 need a strong one. When unsure, inherit the session model.

## 3. Standard finding format

Every finding from every pass uses this block (the pass files repeat it; passes 17 and 18 extend the header to a location pair, since their findings always name both sides):

```
[P<NN>] <SEVERITY> <file>:<line>
Claim: one-sentence concrete defect
Evidence: the code or trace that shows it
Failure: the concrete bad outcome, and who observes it
Fix: the missing defense, named
```

SEVERITY is CRITICAL, HIGH, MEDIUM, LOW or NIT. A finding that depends on code, schema or config you cannot see gets tagged `NEEDS-CONTEXT` with the exact files/facts needed. Passes that demand per-item verdicts (01 entry points, 03 endpoints, 11 call-site table, 12 classification, 18 coverage list) must report clean items too; "clean" is a result, not an omission.

## 4. Pass 20 and the gate

Pass 20 may not add findings. It re-locates every citation, tries to falsify each finding against existing guards, merges duplicates, re-scores severity conservatively, and outputs three sections: CONFIRMED (sorted by severity), UNVERIFIED (with the exact context needed), REJECTED (one-line reasons).

**20/20 means both of:**

1. All 20 passes executed (CLEAN and N-A verdicts count as executed).
2. Zero CONFIRMED findings remain unfixed or unwaived (a waiver is user-granted, explicit, and recorded in the report).

Fix loop: fix CONFIRMED findings in severity order. Re-run the originating pass over the fixed code, then pass 20 over any new findings. Repeat until CONFIRMED is empty. UNVERIFIED items do not block the gate but must appear in the final report with what is needed to resolve them. Only the user can waive a CONFIRMED finding, explicitly; record the waiver in the report. Never waive one yourself to reach 20/20.

## 5. Report

ALWAYS end with this structure (conversation language for prose, keep the section skeleton):

```
# audit-20 report - <scope>, <date>

## Scoreboard
| # | Pass | Verdict |
(verdict per pass: CLEAN / N-A (reason) / FIXED(n) / WAIVED(n) / BLOCKED(n))

Verdict mapping: a pass whose findings were all REJECTED by pass 20 scores CLEAN
(the rejections still appear in the Rejected section); all-UNVERIFIED scores
CLEAN + UNVERIFIED(n) annotation; pass 20's own row records its summary counts
(CONFIRMED/UNVERIFIED/REJECTED) instead of a verdict.

## Confirmed and fixed
(each finding + how it was fixed)

## Unverified
(finding + exact missing context)

## Rejected
(one line each, from pass 20)

## Gate
PASSED 20/20  |  BLOCKED (list what remains)
```

## Pass index

| # | File | Hunts for |
|---|------|-----------|
| 01 | `passes/01-injection-untrusted-input.md` | untrusted data reaching sinks unvalidated/unencoded |
| 02 | `passes/02-authentication-session.md` | identity establishment and maintenance flaws |
| 03 | `passes/03-authorization-idor.md` | missing server-side ownership/role checks, IDOR |
| 04 | `passes/04-secrets-exposure.md` | hardcoded secrets, PII in logs, leaky errors/responses |
| 05 | `passes/05-error-handling-failure-paths.md` | swallowed errors, partial writes, corrupt-state continuations |
| 06 | `passes/06-concurrency-races.md` | races, check-then-act, missing awaits, deadlocks |
| 07 | `passes/07-resource-lifecycle-leaks.md` | unreleased connections, handles, timers, listeners, locks |
| 08 | `passes/08-data-access-n-plus-1.md` | N+1, unbounded queries, in-app filtering, lock contention |
| 09 | `passes/09-complexity-hot-paths.md` | accidental O(n²), recomputation, redundant round-trips |
| 10 | `passes/10-memory-unbounded-growth.md` | evictionless caches, growing globals, retained graphs |
| 11 | `passes/11-external-calls-resilience.md` | missing timeouts, unbounded retries, undefined failure behavior |
| 12 | `passes/12-idempotency-retry-safety.md` | duplicate effects under retry/redelivery |
| 13 | `passes/13-transactions-consistency.md` | multi-write operations without transaction/saga/outbox |
| 14 | `passes/14-config-environment-hardening.md` | dangerous defaults, env divergence, missing fail-fast |
| 15 | `passes/15-dependency-supply-chain.md` | unused/duplicated/abandoned deps, install scripts, typosquats |
| 16 | `passes/16-logging-observability.md` | diagnostic blind spots, missing audit trail, log noise |
| 17 | `passes/17-api-contract-consistency.md` | inconsistent naming, errors, status codes, pagination |
| 18 | `passes/18-cross-module-contracts.md` | responsibility gaps and double effects across boundaries |
| 19 | `passes/19-test-gap-assertion-quality.md` | missing failing-path tests, assertion-free tests |
| 20 | `passes/20-verification-filter.md` | false positives in passes 1-19; produces the final verdict |

Source: the 20-pass audit prompt by Ersin Koç ([@ersinkoc](https://x.com/ersinkoc/status/2074552091826413695)), converted into this skill set.
