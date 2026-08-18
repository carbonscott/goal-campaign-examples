# auto-campaign-examples

Working examples for running a **`/goal` campaign** in Claude Code: a long-lived,
multi-iteration loop where an orchestrator delegates to subagents, records every
claim with its provenance, and stops on a condition rather than on a turn count.

Two things live here:

1. **`goal-cast/`** — reusable, domain-agnostic *cast* templates. A cast declares
   the roles a campaign delegates to (who advises, who executes, who verifies),
   the discipline they operate under, and the delegation budget. You paste one
   into a `/make-goal` prompt, fill the `[slots]`, and it becomes part of the
   generated contract.
2. **`optimize-throughput/`** — the complete on-disk artifacts of one real
   campaign that ran to 21 iterations, kept verbatim as a worked example of what
   these files look like after a campaign has actually been driven.

## `goal-cast/` — the two casts

| File | Shape | Roles |
|---|---|---|
| `advisor-worker.goal_cast.md` | General campaign: research, build, verify a deliverable | orchestrator, planner advisor, skeptic advisor, workers, cold-reader |
| `implementer-runner.goal_cast.md` | "Beat the current best record" | orchestrator, 1–3 implementers, exactly 1 runner |

They differ in where verification lives. The **advisor-worker** cast buys it with
delegations: a *planner* attacks the decomposition before it is committed, a
*skeptic* tries to refute each claim after results but before belief, and a *cold
reader* is handed the deliverable with no context and told to use it. The
**implementer-runner** cast spends its whole budget on candidates instead and
pushes the skeptic's duty onto the orchestrator, buying rigor structurally
instead: whoever writes a solution never reports its score, every candidate is
measured at a committed SHA, and the champion is re-measured as a control in the
same iteration as its challengers.

Rules both casts share, and the reason for each:

- **Predict before you measure.** The expected value goes in the ledger before
  the run starts. A band written afterwards always agrees and proves nothing.
- **Evidence, not conclusions.** Workers return SHAs, paths, raw output, and an
  explicit list of what they did *not* check. A stated gap is useful; a hidden
  one is poison.
- **Quarantine has teeth.** A refuted claim cannot be used as a premise in a
  later iteration until new evidence resolves the objection.
- **One writer per scarce resource** — GPU, deploy target, shared file, git
  index — serialized by an explicit gate.

## `optimize-throughput/` — a campaign's artifacts

The campaign: beat the steady-state out-of-core throughput of a randomized-SVD
pipeline on one L40S GPU, streaming a fixed 1.546 TB store (2.05× the node's RAM)
from NVMe through the host to the device. The record metric is GB consumed
end-to-end inside a 300 s steady-state window, at a fixed algorithm, with a
numerics gate passing.

It ran 21 iterations and moved the record **4069 → 10326 GB (2.54×)** across seven
champions:

| Iteration | Candidate | GB in window |
|---|---|---|
| 1 | `origin-p3` (baseline) | 4069 |
| 2 | `c2b-register` | 6729 |
| 3 | `c3a-bf16` | 9891 |
| 7 | `c7b-convert4` | 9909 |
| 12 | `c12-convert4-r3` | 10158 |
| 14 | `c14-convert8-r3` | 10265 |
| 18 | `b18-d4s4-3` | 10326 |

The files, and what each is for:

| File | Role |
|---|---|
| `*.json` (contract) | The goal, its context, the operating mode, `done_when`, guardrails, and bound |
| `*.goal_cast.json` | The campaign-specific cast, derived from the generic one |
| `*.setup.json` | Standing facts about the environment — hardware, storage tiers, Slurm, the harness — each tagged VERIFIED or INHERITED |
| `*.ledger.json` | Per-iteration record: what was tried, predicted, measured, believed, and overruled |
| `*.claims.json` | Every claim with its provenance and its skeptic verdict |
| `*.scoreboard.tsv` | Append-only, one row per measured candidate; the latest `record=Y` row is the champion |

`*.setup.json` and `*.ledger.json` are the interesting ones to read: the setup
file shows facts being *promoted* from inherited to verified as the campaign
re-observes them, and the ledger shows corrections landing as new entries rather
than edits to old ones.

The absolute paths in these files point at a SLAC S3DF project directory and are
kept as-written for fidelity; nothing here reads or needs them.

## Using a cast

```
/make-goal
```

Describe the goal, then paste the body of whichever `.goal_cast.md` fits the
shape of the work and fill the `[slots]`. `/make-goal` interviews you, writes the
contract and bootstraps the ledger, then prints the `/goal` command to run.

Keep "prefer the Workflow tool over the Agent tool" in the prompt if any role
sets `effort="..."` — the Agent tool accepts a model override but not a reasoning
effort; only Workflow sets effort per role.
