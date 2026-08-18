# goal-campaign-examples

Examples of goal-campaign contracts and the prompts that produced them.

## Recommendation

Use `/make-goal` **in plan mode** to create campaign contracts.

Plan mode brings its own planning workflow — parallel exploration of the codebase
and a design pass — and `/make-goal` gets to use it. That makes the contract more
resourceful: it pulls in what's actually available (existing code, data, tools,
prior runs) and folds it in where it makes sense, instead of only encoding what
came up in the interview.

(Fable may not need plan mode — it tends to be resourceful on its own.)

## Layout

- `goal-cast/` — goal-cast templates (advisor/worker, implementer/runner)
- `optimize-throughput/` — a worked campaign: the prompts, the contract, and the ledger/claims/scoreboard it produced
- `delegation-guard.md` — prompt template for guarding delegation

Early stage; expect this to change.
