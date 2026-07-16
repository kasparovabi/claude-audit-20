---
name: guard-20
description: The write-time companion to audit-20: the same twenty failure classes, applied as rules WHILE writing code instead of hunted afterwards. Load before touching code on any coding task (feature, fix, refactor, generated code). Not an audit; spawns no agents and no fleet, costs no extra tokens; its job is to make the final audit-20 run come back mostly CLEAN. Trigger: writing code, implement, fix, refactor, "guard-20", "coding rules".
---

# guard-20: don't find the bug, never write it

audit-20 hunts defects in finished code; that is expensive. guard-20 turns the same
twenty defect classes into writing discipline: rule numbers map one-to-one to the audit
passes, so when the final audit runs, it passes clean.

This skill launches no agents, workflows, or scans. It is a set of rules you follow
while writing, plus a two-minute self-check at the end.

## Before writing (30 seconds)

Answer four questions; the answers tell you which rules are hot for this task:

1. Where does external data enter? (params, headers, body, files, queues, third-party responses) → G1, G4
2. Which stores / external services does this touch? → G8, G11, G12, G13
3. Any multi-step writes or shared state? → G5, G6, G7, G10
4. Who calls this code, and what do they see on failure? → G3, G16, G17, G18

## The 20 rules (while writing)

**Security (G1-G4)**
- G1 Validate or context-encode external input before it reaches a sink (query, exec,
  file path, HTML, redirect). Headers are input too: the first element of
  x-forwarded-for, user-agent, and referer are text the client wrote; never key a
  trust decision on them.
- G2 If it's auth code: token generation needs entropy + expiry + rotation; logout must
  actually revoke; carry cookies with their attributes (Secure/SameSite/Max-Age), never
  reduce them to name+value.
- G3 Every operation touching a resource checks ownership/role server-side, BEFORE the
  action, on every path (bulk/export/admin included). Never use a client-supplied ID
  unchecked.
- G4 Secrets and PII never enter code, logs, test fixtures, or example env files. No
  secrets in client-visible names (NEXT_PUBLIC_* and friends). Error responses must not
  leak stacks, queries, or internal paths.

**Failure and concurrency (G5-G7)**
- G5 Empty catch blocks are forbidden. If step N fails, name the state steps 1..N-1
  left behind: roll back, compensate, or degrade deliberately + log. Don't swallow the
  framework's control-flow errors (e.g. Next's dynamic bail-out); rethrow them.
- G6 Don't write check-then-act; pick the atomic equivalent (unique constraint, upsert,
  CAS). No fire-and-forget on calls with side effects. Freeze UI inputs while a long
  async job runs.
- G7 Release every resource you acquire (connection, handle, timer, listener, lock) via
  finally/defer/cleanup; if you write an effect, write its cleanup in the same moment.

**Data and scale (G8-G10)**
- G8 No queries inside loops; batch. No unbounded query against a table that grows.
  Filtering and sorting are the store's job, not application memory's.
- G9 No unbounded nested loops on hot paths; hoist loop invariants. Equally: don't
  optimize paths that will never see scale.
- G10 If you write a module-scope collection or cache that grows, write its
  TTL/eviction/cap in the SAME change; "I'll add it later" is an audit finding.

**External dependencies (G11-G13)**
- G11 Every out-of-process call gets an explicit timeout. Retries, if needed: bounded,
  with backoff, only on idempotent operations. Choose deliberately what the user sees
  when the dependency is down.
- G12 Write every state-changing operation as if it will be delivered twice: if it is
  not naturally idempotent, add an idempotency key or constraint.
- G13 Don't write an operation that updates more than one thing (two tables, DB+cache,
  DB+event) without a transaction/saga/outbox. Never update the cache before the commit.

**Environment and supply chain (G14-G15)**
- G14 No dangerous defaults (debug on, permissive CORS, TLS verification off). Missing
  required config never runs silently wrong: fail fast, or degrade deliberately with a
  warning log.
- G15 A new dependency is the last resort (the ladder: stdlib > platform > existing
  dep). If you add one, check its advisories; pin exact versions on security-critical
  paths (auth/crypto/untrusted-input parsing).

**Observability and contracts (G16-G18)**
- G16 Leave no silent failure points: every error branch gets ONE log line with context
  (correlation/IDs yes, PII/secrets no). Security events (login failures, permission
  denials) leave a trail.
- G17 When touching an API surface, copy the existing contract: same error shape, same
  status codes, same naming. Never create a second shape for the same concept.
- G18 At module boundaries, assign each responsibility to ONE side and make it visible
  (who validates, who retries, who owns the transaction). If both sides do it, delete
  one; if neither does, close the gap.

**Tests and closing (G19-G20)**
- G19 If you wrote a branch/parser/money/security path, write the smallest failing test
  in the same session; at least one non-happy-path assert. No tests for trivial
  one-liners (YAGNI applies to tests too).
- G20 Before finishing, self-scan (≤2 min, no tools): look at your diff, pick the rules
  it touches, and give each a one-line "applied/clean". The report is a single line:
  `guard-20: [G1,G5,G11] applied; rest N-A`.

## Relationship to lazy-coding styles

A minimalism skill says shorten; guard-20 says what may never be shortened away:
validation at trust boundaries, failure paths, timeouts, resource release, idempotency.
On conflict, guard-20 wins; these are the classic "never simplify away" items.

## Relationship to audit-20

guard-20 does not replace the audit; run audit-20 ONCE at the final milestone (when the
user declares the project final, or asks for it). Code written to these rules makes most
passes come back CLEAN, so the audit closes cheap. Between iterations, don't run the
fleet: typecheck + tests + build + smoke are enough.
