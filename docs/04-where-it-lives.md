# 04 — Where it lives: the implementation map (generic)

One table. If a concept is not in this table, it is not part of
the pattern — it is an idea. Status words mean exactly this, for
the pattern itself (see README):

- **Live pattern** — fully specified in this doc, ready to
  implement as written. Not a claim that any particular project
  runs it today.
- **Planned** — written here as future work, not yet part of the
  core pattern. Do not cite it as a capability.
- **Parent-owned gate** — a human (the parent agent) does it; no
  worker claim counts.

| Concept | Concept source | Where it lives in a typical forge | Status |
|---|---|---|---|
| Deterministic control plane | Graph §1 | `forge.py`; one transition per run; quiescent loop drains ready actions, max 8 per run, until WAIT/NOOP | Live pattern |
| States IDLE→MERGED→IDLE | Graph §2 | `STATE.json` + control plane | Live pattern |
| Three-file contract | Graph §2 | Frozen rules / mutable branch + progress / org-code brief | Live pattern |
| Untrusted-proposer / trusted-verifier | Graph §3 | Vet files, verdict on line 1, written by a fresh agent | Live pattern |
| Vet-gated verify | Harness (generator≠evaluator) | `verify` prints `CHECKS PASS (vet pending)`; `verify --require-vet` prints `VERIFY PASS (vet-verified)` only on a ship verdict, else `VERIFY FAIL` | Live pattern |
| Completed-list sequencing | Graph (routing) | `next_uncompleted()`; `completed` list; claim refuses completed items | Live pattern |
| Markdown coordination | Graph §4 | `WORKER_BRIEF.md`, checklists, progress notes | Live pattern |
| Single metric + ratchet | Graph §5 | One build metric (e.g. verified items merged per week, target ≥ 1); main branch always deployable | Live pattern |
| Macro actions + parallelism | Graph §6 | One item per worker; parallel only on disjoint file sets | Live pattern |
| Memory outside context | Graph §7 | `STATE.json`, `JOURNAL.md`, `progress/<item>.md`, `vet/<item>.md`, `runs/` | Live pattern |
| Heartbeat + supervisor | Harness (guides+sensors) | `heartbeat-check`; touch ≤10 min, stale >45 min in `IMPLEMENTING` = wedged, resume | Live pattern |
| Foreground-only exec | Harness (survival) | Worker brief rule; servers tested in one foreground command that kills the server | Live pattern (rule; enforcement is by brief) |
| Verify artifact, not report | Harness §2 | Remote tip + tree re-read via API after every push; sent mail / committed row re-read | Live pattern |
| Leave crumbs | Harness §5 | `JOURNAL.md`, `FAILURES.md`, `runs/` | Live pattern |
| Session-start ritual | Harness §6 | Worker first act: read state + progress + remote state | Live pattern |
| Re-derive per release | Harness §7 | Weekly retro question: which scaffolding still earns its keep? | Live pattern |
| Weekly retro | Harness (self-improvement) | Scheduled retro (cadence is a project choice); one structural control per failure | Live pattern |
| Worker proposals | Harness (self-improvement) | `PROCESS_CHANGELOG.md`, triaged by retro: apply / adapt / reject with reason | Live pattern |
| Selection loop (select→build→vet→reward) | Harness (self-improvement) | Daily pattern (optional): bandit scoring, ship +1 / nits +0.5 / rework −1, chat ±2, EMA multipliers | Live pattern (optional) |
| Parent browser gate | Harness (verify artifact) | Checklist item + evidence file; parent drives the real built output | Parent-owned gate |
| Real-device gate | Harness (verify artifact) | Install on a real phone, offline reload after kill | Parent-owned gate |
| Push discipline | Harness (verify artifact) | Staging repo + API verify; seed-empty workaround; `--from`/`--base` with full SHAs; `--from` replay quirk; never pipe through `tail` | Live pattern |
| Continuous loop (no waiting) | Standing rule | Routine decisions do not wait; harness owns delays; only safety gates + user-only choices stop the line | Live pattern |
| Parallel subagents | Graph §6 | Parent spawns all independent agents at once in one turn; disjoint files/branches/accounts/browsers only | Live pattern |
| Parallel-track list in the control plane | Graph §6 | A `parallel` list in state, separate from the main claim list | Planned — until it exists, the parent is the sole writer of parallel-track state |
| Tester deployment gate | Project safety rules | Vet + verify + merge + staging drive, then deploy + changelog; tester opt-in required | Parent-owned gate |

## How to use this table

- Pick a concept. Read across: concept source (why), typical
  forge location (how), status (whether to trust it yet).
- If status is **Planned**, do not cite it as a capability.
  If status is **Parent-owned gate**, do not let a worker claim
  it closed.
- If a row says "rule; enforcement is by brief", the check is
  a human reading the brief, not code. That is weaker than a
  code gate, and the table says so instead of hiding it.
- To apply this table to a real project, copy it and fill in
  that project's actual file paths and schedule choices. This
  generic table is the template, not the filled-in copy.
