# 04 — Where it lives: the implementation map

One table. If a concept is not in this table, it is not
implemented — it is an idea. Status words mean exactly this:

- **Live** — running, with evidence on file.
- **Planned** — written in a plan or architecture doc, not yet running.
- **Parent-owned gate** — a human (the parent agent) does it; no worker claim counts.

| Concept | Skill | Forge | BJJ example | Flo example | Status |
|---|---|---|---|---|---|
| Deterministic control plane | Graph §1 | `forge.py`, `flo-forge.py` | Quiescent loop, max 8 actions/run, 5-min cadence | Parent-orchestrated sequence, 2026-10-24 | Live (BJJ cron; Flo parent-run) |
| States IDLE→MERGED→IDLE | Graph §2 | `STATE.json` | Same state list | Same state list | Live |
| Three-file contract | Graph §2 | Frozen / Mutable / Org code | `forge.py` + config frozen; branch + progress mutable; `WORKER_BRIEF.md` org | Plan §§2,5,7 + `flo-forge.py` + `config.json` frozen; app tree + progress mutable; `WORKER_BRIEF.md` org | Live |
| Untrusted-proposer / trusted-verifier | Graph §3 | Vet files | Vet agent = standing reviewer; verdict on line 1 | Fresh vet per workstream; verdict on line 1 | Live |
| Vet-gated verify | Harness (generator≠evaluator) | `verify` / `verify --require-vet` | `verify` checks CI + review on head SHA | `CHECKS PASS (vet pending)` vs `VERIFY PASS (vet-verified)`; `test_verify.py` 5 paths | Live (Flo split; BJJ review gate) |
| Completed-list sequencing | Graph (routing) | `next_uncompleted()` | Frozen priority list + issue discovery (§6d) | `completed: []`, `CLAIM_REFUSED` for completed | Live (Flo fix; BJJ discovery) |
| Markdown coordination | Graph §4 | `WORKER_BRIEF.md`, checklists | `WORKER_BRIEF.md`, progress notes | `WORKER_BRIEF.md`, `FEATURE_CHECKLIST.md` | Live |
| Single metric + ratchet | Graph §5 | Build metric | Issues closed/week, target ≥ 1; main always green | Verified checklist items/week, target ≥ 1; main always deployable | Live |
| Macro actions + parallelism | Graph §6 | One item per worker | §6b parallel tracks, `parallel` list in STATE | §8 parallel work; disjoint file sets only | Live (BJJ list; Flo parent-enforced, code not yet in `flo-forge.py`) |
| Memory outside context | Graph §7 | STATE, JOURNAL, progress, vet | `STATE.json`, `JOURNAL.md`, `progress/<issue>.md` | `STATE.json`, `JOURNAL.md`, `progress/<ws>.md`, `vet/<ws>.md`, `runs/` | Live |
| Heartbeat + supervisor | Harness (guides+sensors) | `heartbeat-check` | Touch ≤10 min, stale >45 min = wedged, resume | Same thresholds | Live |
| Foreground-only exec | Harness (survival) | Worker brief rule | Briefed to every worker | Briefed to every worker | Live (rule; enforcement is by brief) |
| Verify artifact, not report | Harness §2 | API re-read after push | PR head SHA, check-runs, reviews re-read via API | Remote tip + tree re-read via API after every push | Live |
| Leave crumbs | Harness §5 | JOURNAL, FAILURES, runs | `JOURNAL.md`, `FAILURES.md`, `runs/` | `JOURNAL.md`, `runs/`; `FAILURES.md` planned, retro-owned | Live, except Flo `FAILURES.md` planned |
| Session-start ritual | Harness §6 | Worker first act | Read STATE + progress + `git fetch` | Read STATE + progress + repo state via API | Live |
| Re-derive per release | Harness §7 | Weekly retro question | Retro asks which scaffolding to delete | Retro asks which scaffolding to delete | Live (BJJ retro); Flo retro planned |
| Weekly retro | Harness (self-improvement) | Cron / planned | Sun 09:30 ET cron | Planned Sundays, not wired as a cron yet | Live (BJJ); Planned (Flo) |
| Worker proposals | Harness (self-improvement) | `PROCESS_CHANGELOG.md` | Triaged by retro: apply / adapt / reject with reason | Same, triaged by retro | Live (BJJ); Flo planned with retro |
| RL loop (select→build→vet→reward) | — (AGENTS.md standing rule) | Daily cron | `bjj-rl-loop`: bandit scoring, ship +1 / nits +0.5 / rework −1, chat ±2, EMA multipliers | Not a Flo loop | Live (BJJ only) |
| Parent browser gate | Harness (verify artifact) | Checklist item + evidence file | — (repo work; CI + review are the gates) | Parent Chromium headless drive of `dist/`; WS0 closed, WS1 pending | Parent-owned gate |
| Real-phone install gate | — (Flo plan) | WS5 workstream | — | iPhone Safari, Add to Home Screen, offline reload | Planned |
| GitHub push discipline | Harness (verify artifact) | Staging repo + API verify | Staging discipline, default-branch check | Staging repo, seed-empty workaround, `--from`/`--base`, `--from` replay quirk, never pipe through `tail` | Live |
| Continuous loop (no waiting) | — (standing instruction 2026-10-24) | Standing rule | Continuous building is the norm; spawn now, never wait for next tick | Routine decisions do not wait; harness owns delays; only safety gates + user-only choices stop the line | Live (rule) |
| Parallel subagents (general) | Graph §6 | `subagent.spawn`, one turn | Parallel agents on separate modules/directories | WS1 vet + architecture update in parallel, disjoint files | Live |
| Tester deployment gate | — (Flo plan §6D) | Release gates 1–6 | — | Vet + verify + merge + staging drive, then deploy + changelog; tester opt-in required | Parent-owned gate |

## How to use this table

- Pick a concept. Read across: skill (why), forge (how),
  BJJ/Flo (where it actually ran), status (whether to trust it
  yet).
- If status is **Planned**, do not cite it as a capability.
  If status is **Parent-owned gate**, do not let a worker claim
  it closed.
- If a row says "rule; enforcement is by brief", the check is
  a human reading the brief, not code. That is weaker than a
  code gate, and the table says so instead of hiding it.
