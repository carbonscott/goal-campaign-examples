# goal-campaign-examples

Examples of goal-campaign contracts and the prompts that produced them.

## Recommendation

Use `/make-goal` **in plan mode** to create campaign contracts. Plan mode keeps the
interview from turning into premature edits, so the contract gets fully specified
before anything runs.

Running it there also leverages plan mode's own planning workflow — the codebase
research and design pass that plan mode runs — so the contract is grounded in what
the repo actually looks like rather than in the interview alone.

(Fable may not need plan mode — it tends to hold the interview without it.)

## Layout

- `goal-cast/` — goal-cast templates (advisor/worker, implementer/runner)
- `optimize-throughput/` — a worked campaign: the prompts, the contract, and the ledger/claims/scoreboard it produced
- `delegation-guard.md` — prompt template for guarding delegation

Early stage; expect this to change.
