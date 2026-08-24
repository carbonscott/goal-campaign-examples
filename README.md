# goal-campaign-examples

Examples of goal-campaign contracts and the prompts that produced them.

## Recommendation

Use `/make-goal` **in plan mode** to create campaign contracts.

Plan mode brings its own planning workflow — parallel exploration and a design
pass — and `/make-goal` gets to use it. That makes the contract more resourceful:
it pulls in what the situation actually offers (existing code, data, hardware,
tools, prior runs, whatever else is around) and folds it in where it makes sense,
instead of only encoding what came up in the interview.

(Fable may not need plan mode — it tends to be resourceful on its own.)

## Layout

- `goal-cast/` — goal-cast templates (advisor/worker, implementer/runner, reviewer/fixer, actor/judge)
- `optimize-throughput/` — a worked campaign: the prompts, the contract, and the ledger/claims/scoreboard it produced
- `delegation-guard.md` — prompt template for guarding delegation

Early stage; expect this to change.
