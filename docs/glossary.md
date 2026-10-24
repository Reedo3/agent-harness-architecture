# Glossary — every jargon term, in plain words

| Term | Plain meaning | Where to see it working |
|---|---|---|
| **Agent** | The model plus everything around it (instructions, tools, permissions, state, checks). | Harness skill thesis: Agent = Model + Harness. |
| **Graph** | The written-down shape of the work: steps, transitions, gates, in code and markdown. | [01](01-graph-engineering.md) |
| **Harness** | Everything around the model except the model: guides, sensors, verification, recovery. | [02](02-harness-engineering.md) |
| **Control plane** | A small deterministic program that owns state transitions. Not an agent; cannot be talked out of its rules. | `forge.py` |
| **State** | Where one item of work is right now (e.g. `IN_REVIEW`). | `STATE.json` |
| **Claim** | Reserving one item of work so no other worker takes it. | `claim` / `advance` → `CLAIM_NEXT` |
| **Work item / workstream** | One chunk of work, e.g. the forge skeleton, then the core logging feature. One per worker. | [01](01-graph-engineering.md) |
| **Frozen file** | Rules the worker may read but never touch: state machine, priority list, safety rules. | Three-file contract, [01](01-graph-engineering.md) |
| **Mutable file** | The one thing the worker may change: its branch, progress note, proposal. | Three-file contract |
| **Org code** | Markdown instructions the parent iterates: scope, heartbeat, stop conditions, remediation. | `org-code/WORKER_BRIEF.md` |
| **Untrusted-proposer / trusted-verifier** | Anyone may propose work; only a trusted, cheap, deterministic check (plus a fresh vet) may accept it. | Vet files, `verify --require-vet` |
| **Generator ≠ evaluator** | The builder never certifies its own work. A different agent re-reads it as a skeptic. | Every vet file; harness doctrine 3 |
| **Vet** | A fresh senior-reviewer agent that cold-reads the real diff and writes `verdict: ship \| ship-with-nits \| rework` on line 1. | `harness/vet/<item>.md` |
| **Ship-with-nits** | Accepted, with majors recorded as gates and nits carried forward. Not a merge, not a deploy. | [05](05-decisions.md), Decision 3 |
| **Verify** | The deterministic check step. Without `--require-vet` it prints only `CHECKS PASS (vet pending)`. With it, and a ship verdict, it prints `VERIFY PASS (vet-verified)`. | `forge.py verify` |
| **Ratchet** | A rule that only allows forward movement keeping the main branch good. A breaking merge is a revert. | [01](01-graph-engineering.md) |
| **Single scalar metric** | One number for "better". Generic example: verified checklist items merged per week, target ≥ 1. | [01](01-graph-engineering.md) |
| **Macro action** | Delegating a whole feature, not a line of code. | Graph skill §6 |
| **Heartbeat** | A file a worker touches at least every 10 minutes to prove it is alive. Older than 45 minutes in `IMPLEMENTING` = wedged. | `heartbeats/<item>` |
| **Supervisor** | A process that watches a worker and restarts it on death, with backoff. | [02](02-harness-engineering.md) |
| **Crumbs** | Timestamped logs, why-notes, decision records left so a future session can resume. | `JOURNAL.md`, `progress/<item>.md`, `FAILURES.md` |
| **Resume, never restart** | A dead worker's replacement starts from its progress note and branch, not from zero. | Heartbeat + supervisor sections |
| **Quiescent loop** | A run that drains all ready actions (claim→spawn, vet→merge→claim→spawn) until WAIT/NOOP, max 8 actions, instead of doing one action per run. | [01](01-graph-engineering.md) |
| **Backpressure** | Tests/lint/typecheck wired to reject bad output automatically: success silent, failure verbose and instructive. | [02](02-harness-engineering.md) |
| **Sensor** | A check read after acting: heartbeat age, test exit, privacy-check result, vet verdict, remote tip. A sensor with a hole is worse than no sensor, because a green check gets cited as proof. | [02](02-harness-engineering.md) |
| **Guide** | Steering before acting: a clear brief, context, task structure, remediation steps. | Harness doctrine 4 |
| **Retro** | The weekly self-improvement review: failures → exactly one structural control each, recorded as a failure→fix mapping. | [03](03-self-improvement.md) |
| **Selection loop** | A loop that picks the next feature by bandit-style scoring, builds it, vets it, and updates multipliers from the reward. Optional; a fixed priority list is an alternative. | [03](03-self-improvement.md), Loop 4 |
| **Bandit scoring** | `user_value × feasibility × evidence × learned type_multiplier`. The multiplier learns from past vet rewards. | Selection loop |
| **Reward** | Vet outcome as a number: ship +1, ship-with-nits +0.5, rework −1; human chat feedback ±2. | Selection loop |
| **Parallel track** | A second worker running concurrently on a non-interfering work item (disjoint files). Tracked in a `parallel` list, never in the main claim list. | [05](05-decisions.md), Decision 1 |
| **Serial gate** | A gate that must happen in order, one at a time, parent-owned: vet, browser drive, merge, push, deploy. Never parallel. | [05](05-decisions.md) |
| **Browser gate** | Parent-owned click-through of the real built output, with an evidence file. A worker's headless run never closes it. | [02](02-harness-engineering.md) |
| **Staging repo** | A dedicated git repo used only for pushing, separate from a working directory that is not a git repo. Contains only source + harness + plan, never `node_modules`/`dist`. | [02](02-harness-engineering.md), push discipline |
| **API verification** | Re-reading the remote repo via the authenticated API (visibility, branch, tip + parent, tree) before reporting a push as landed. | [02](02-harness-engineering.md), [05](05-decisions.md) |
| **Synthetic data** | Invented test data, used in code, tests, and previews so no real person's data ever appears in a repo, commit, or log. | [02](02-harness-engineering.md) |
