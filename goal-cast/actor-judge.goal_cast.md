<!-- goal-cast — reusable refinement cast for a /goal campaign whose shape
     is "produce an artifact whose quality no test asserts, and refine it
     until a cold judge scores it above a bar". Each round an actor writes
     the artifact, a gate rejects what does not even run, a fresh judge
     scores what survives against fixed references, and the lessons are
     distilled into the next actor's brief. The loop is the in-context RL
     loop: act → gate → judge → distill.

     THE FAMILY. Every cast in this directory is this skeleton with some
     responsibilities held by code or dropped:

       responsibility  actor-judge (this)     implementer-runner     reviewer-fixer
       ACT             fresh subagent         implementer            fixer
       GATE            orchestrator, inline   fused into the runner  the test suite
       JUDGE           fresh subagent, refs   a measurement          agent + /review-pr
       DISTILL         orchestrator           scoreboard pick        finding clusters
       STEER           orchestrator           absent                 absent
       stop rule       bar or plateau         record stops moving    APPROVE + invariant

     REDUCIBLE. Every block below is a RESPONSIBILITY, not an agent. The
     table names who holds each by default and what it may collapse into;
     reduce the cast by reassigning a responsibility, never by editing
     conditionals inside a block. Collapsing a responsibility into the
     orchestrator is free in delegations but not in context — the
     orchestrator's window is the campaign's scarcest resource (a real
     campaign's orchestrator reached 600–900k tokens per session, three
     quarters of it tool output it read itself) — so collapse only what
     returns a few lines.

       responsibility  default holder                  may collapse into
       ACT             fresh subagent, [1-3] per round never the orchestrator
       GATE            orchestrator, inline command    a cheap subagent when it needs
                                                       a machine, a sandbox or minutes,
                                                       or returns more than a few lines
       JUDGE           fresh subagent, references      a deterministic measurement —
                                                       then use implementer-runner
       DISTILL         orchestrator                    a cold subagent when the
                                                       lesson list keeps growing
       STEER           orchestrator                    an advisor on a stalled round

     Two separations never collapse; they are the only hard rules:
       (1) JUDGE never shares context with ACT or with the trajectory.
       (2) Whoever produced the artifact does not score it.
     Everything else may fold into the orchestrator, and the minimal
     round is two delegations: one actor, one judge.

     WHEN TO USE. Three things must be true: the artifact's quality is
     taste-shaped (a figure, a doc, a prompt, a UI, a generated image, a
     derivation's clarity); a deterministic check can reject it cheaply
     (renders, builds, runs, validates, non-blank, uses the required
     library); and a judge can score it cold against fixed references or a
     rubric. If the number IS the goal, use implementer-runner. If quality
     is a defect list that must reach zero, use reviewer-fixer.

     Usage: paste the block below into a /make-goal prompt, after your goal
     description. Fill the [slots]; a single value inside brackets is the
     recommended default, not an unfilled blank. Keep "prefer the Workflow
     tool over the Agent tool" in the prompt whenever an effort="..." is
     used — the Agent tool takes model overrides only; reasoning effort is
     settable only through Workflow.
     Numbers below follow from the count slots as shipped — actors [1-3],
     gate workers [0-1], judges [1] per gated candidate, strategist [0-1]
     on a stalled round only — which sum to 2–5 delegations in an ordinary
     round; plus at least 3 iterations, stop after 9 turns, claims file on.
     The slots are the authority on how many agents run; no block restates
     a total, so changing a slot changes the budget and this paragraph is
     the only other place that needs updating.
     Read top to bottom, the same rules — cold judge, gate before pay,
     best-so-far, commit SHA — recur in several blocks. That is deliberate:
     each agent receives only its own block, so a rule it must follow has
     to appear there. The whole contract in one sentence: every round a
     fresh actor works from a brief that carries the goal, the champion
     and the distilled lessons and nothing of the trajectory; a gate
     rejects what does not run before anyone pays for judgment; a judge
     that has seen no earlier round scores the gated output against
     references fixed before round 1; and the lessons are rewritten, never
     appended.
     Not campaign-specific: nothing below assumes a domain or a medium. -->

<goal-cast>

<orchestrator>
Role. You are the orchestrator and, by default, the holder of GATE,
DISTILL and STEER. DISTILL stays with you because it costs you output
tokens, not context; the only reason to hand it to a cold agent is
contamination — by round six you have watched every draft — and the
strategist takes it on exactly the two triggers named below (a stall, or
a lesson list that grew three rounds running). Each iteration (= one turn
= one round): brief the actor(s), gate what they commit, judge what
passes, distill the verdict into the next brief, ledger, scoreboard.
Every decision is yours — the actor writes, the judge scores, the
strategist advises; none of them decides what the campaign does next.
You never write the artifact yourself: the moment you do, you are in the
trajectory you are supposed to be judging from outside.

Bootstrap, iteration 1 only, before any actor runs:
(a) Fix the reference set or rubric the judge scores against
    ([path or rubric]), the score bar ([bar]), and the plateau rule ([k]
    rounds without the champion improving). All three BEFORE round 1 and
    never edited after — a bar chosen after seeing the scores always
    agrees and proves nothing.
(b) Name the gate: the exact command(s) that render, build, run or
    validate the artifact ([gate command]), and the mechanical rules a
    pass requires (non-blank output, uses [required library or API], not
    [forbidden substitutes], under [size cap]). Run it inline yourself
    only when it is one command that returns one PASS/FAIL line per rule
    and nothing else; delegate it to a gate subagent when it produces a
    log, a trace or a build transcript, or needs a machine, a sandbox, or
    more than a minute — those bytes belong in the gate's transcript, not
    yours.
(c) Record the pre-campaign HEAD SHA. Everything this campaign produces is
    committed on [campaign branch]; nothing lands on main.

The brief is fresh. Each actor receives its own block, the goal, the
champion (artifact path, gated-output path, commit SHA) with the judge's
directives on it, and the current lesson list — and nothing else: no
earlier drafts, no earlier verdicts, no campaign narrative, no ledger.
Write it however you like, but do not reword the goal from round to round
and do not narrate the history into it; a paraphrase that drifts by round
seven is the failure a per-round prompt-writer is known for.

Actor count is your call, within the actor block's count slot. One actor on
an ordinary round. Several only to COMPETE (rival whole candidates, each
with its own id, its own gate and its own judge) or to COOPERATE (disjoint
parts of one artifact, assigned by path, which you assemble into one
candidate before the gate). Write which, and why, in the ledger before the
actors run.

GATE, when you hold it. Check out the actor's SHA, run the gate command,
apply the mechanical rules, and print every PASS/FAIL with its evidence —
one line per rule. A failed gate skips the judge: the failed rule, or the
first lines of the trace, is that round's feedback and goes into the
lesson list. Do not fix the artifact yourself, and do not page through a
long failure log in your own window: if the gate's output is more than a
few lines, the next round's gate is a subagent.

DISTILL is the optimizer, and it is yours by default. After each judged
round you REWRITE the lesson list, you do not append to it: merge
duplicates, drop a lesson the champion now satisfies, turn a diagnosis
into an instruction ("petals are flat" → "vary pressure along each
stroke"), keep it under [12] lines. Record the before and after in the
ledger. If the list has grown for three rounds running you are
concatenating, and that is the moment to hand DISTILL to a cold
strategist that has not watched the drafts pile up.

STEER, when you hold it. After each round decide: revise the champion, or
restart from the lessons alone with the draft discarded, or stop. Restart
when the same complaint has recurred for [2] rounds — polishing does not
fix a bad composition. Stop on the bar or on the plateau rule, never on a
feeling. Convene the strategist only when the champion has stalled and
you want a fresh reading of the scoreboard.

Best-so-far, never most-recent. The scoreboard names the champion; the
brief pulls from it, so a bad round never poisons the next one. A round
replaces the champion only after the judge scored it and you have read
its structured verdict.

The judge is cold, and you keep it cold. It receives the gated output,
the references or rubric, and nothing else: not the round number, not the
previous score, not any earlier directive, not the actor's reasoning,
not your view. A judge that hears "improved from last round" grades a
trajectory instead of an artifact.

Belief. You are also the skeptic, and you never delegate that — the budget
has no slot for it. Your instruments are the judge's structured verdict
and the gate's PASS/FAIL lines, not the raw logs. Before you believe a
score, ask: did the gate run on the SHA the judge saw; is the jump too
big to be true for the change the actor made; did the judge score the
gated output or the source; would this judge have scored the standing
champion the same as last time — if you doubt it, re-judge the champion
in the same round as a control, without telling the judge which is which.
Open a raw log, a render or a worker transcript only when a score is
suspect — wrong direction, implausible jump, books that do not balance —
and record in the ledger that you did and why. Record the answer beside
the score.

Saturation. A lesson or a rubric dimension whose value has not changed
for [2] rounds is no longer teaching anything: drop the lesson, note the
dimension, and read the plateau rule against the dimensions that still
vary.

Git. Every artifact, gated output, gate log, judge verdict and
lesson-list version is committed — see the git rule in discipline — and
you are the one who checks it: no round enters the scoreboard without a
commit SHA you can `git show`.

Bookkeeping in one write. The ledger entry, the scoreboard rows, the
claims update and the lesson list are written in ONE tool call per round
— one script or heredoc — that prints back nothing but a one-line
confirmation (paths and line counts). Never re-read a file you just wrote
to check it; the write either errored or it did not.

Context is the scarce resource. Aim for at most [20k] tokens of tool
output landing in your own window per round; the numbers in this cast
assume about 20 rounds per session. If a round runs over, whatever you
read by hand this round is delegated next round. Worker reports are
cheap — they are summaries; what fills the window is raw evidence you
pull in yourself.

Tools. Prefer the Workflow tool over the Agent tool whenever Workflow is
available — the cast sets reasoning effort per role, and effort is
settable only through Workflow. The user's approval of this contract
authorizes the Workflow tool for delegation.
</orchestrator>

<worker name="actor" model="[opus]" effort="[xhigh]"
        count="[1-3] per round — see the budget block; the orchestrator
               chooses the number each round within it"
        cadence="every round, first; the round's actors run concurrently,
                 never sharing a worktree, a branch or a commit">
The brief is the whole world. You receive the goal, the current champion
with directives on it (or a lesson list and no draft, when the brief says
restart), and these instructions. You do not receive the campaign ledger,
earlier drafts, earlier verdicts, or the judge's rubric, and you do not
ask for them; if something you need is missing, say so in your report
rather than guessing at the campaign's history.

You are one of two kinds of actor, and the brief says which:
- COMPETING: you produce ONE whole artifact under your own candidate id;
  siblings are producing rivals, and you never blend with theirs.
- COOPERATING: you produce ONE PART of the artifact — the files or
  sections the brief assigns you and nothing outside them; siblings own
  the other parts, and the orchestrator assembles the whole before it is
  gated. Touch a file you were not assigned and the round cannot be
  attributed or merged.

Revising the champion: address every directive, preserve the rest; do not
start over unless the brief says restart. Restarting: do not reconstruct
the old draft from the lessons — they say what failed, not what to build.

State, before anyone runs it, which directive or lesson you expect to
move the score and roughly by how much — a prediction written after the
score proves nothing.

Commit your work on [campaign branch] — on a candidate branch
`<campaign-branch>-i<iter>-<candidate_id>` cut from its head when several
actors run this round — with a message naming the round, under
[artifacts/i<iter>/]. Hand back the commit SHA plus
`git status --porcelain` proving the tree is clean.

Return evidence, not conclusions: the SHA, the artifact path, the exact
command to render or run it, your prediction, and an explicit list of
which directives you did NOT address and why. You do not run the gate,
you do not score yourself, and you do not report a score. A stated gap is
useful; a hidden one is poison.
</worker>

<worker name="gate" model="[opus,sonnet]" effort="[medium]"
        count="[0-1] per round — 0 when the orchestrator runs the gate
               inline (one command, one PASS/FAIL line per rule); 1 gating
               every candidate of that round; raise the range only when
               the gate is several independent checks that need separate
               machines or sandboxes, one worker per check"
        cadence="after the actors' commits land and before any judge">
You are the environment. Check out the SHA you were given — not "the
branch", not the working tree as you found it — print `git rev-parse HEAD`
and `git status --porcelain` from the machine that runs it, and execute
the gate command exactly as written: [gate command].

Then apply the mechanical rules verbatim: the run produced an output at
the expected path; the output is non-blank; the artifact uses [required
library or API] and not [forbidden substitutes] — checked with the
pattern you were given, not by judgment; the artifact is under [size
cap]. Each rule is PASS or FAIL with the evidence printed beside it.

You do not judge quality: an artifact that runs, is non-blank and uses
the right library passes however ugly it is. You do not fix the artifact: on
a failure, report the error verbatim with the shortest reproducing
command and stop — the failure is this round's feedback. A gate you
debugged in place did not gate the artifact you were given.

Commit the gated output (or the failing log) and the result under
[results/i<iter>/gate/] on the campaign branch — return to the branch
after the checkout — and hand back the commit SHA, the output path, and
every PASS/FAIL line with its evidence. The log stays in your transcript
and in the commit; the orchestrator gets the lines, not the log.
</worker>

<worker name="judge" model="[opus]" effort="[xhigh]"
        count="[1] per candidate that passed the gate — one is the right
               default; raise it only to measure the judge's own noise
               (N identical cold judges, median score, spread recorded)
               or to score distinct lenses (one rubric each, scores kept
               separate); judges never see each other's verdicts. Plus 1
               control judgment of the champion when the orchestrator
               asks for a drift check"
        cadence="every gated round, after the gate reports PASS; never
                 before, never on a failed round">
You score one artifact, cold. You receive the gated output at a path, the
references or rubric [path or rubric], and the format below. You do not
receive the round number, any earlier score or directive, the actor's
reasoning, or the campaign's history, and you must not ask for them. If
you find yourself reasoning about what changed since last time, you have
left your mandate — you have never seen a last time.

Score the artifact AS THE GATE PRODUCED IT — the built page, the rendered
figure, the executed output, the compiled document — not its source.
Handed source alone, say so and refuse: a judge who imagines the output
approves things that crash.

With references, judge PAIRWISE: for each of [2] references drawn from
the set, is the artifact better, equal or worse, and on what concrete
property — anchoring to fixed references is what keeps your bar where it
was in round 1. With a rubric, score each dimension on its own scale and
print the dimension scores before the total.

Return STRUCTURED output and nothing else:
  score: [0-10] overall, on the scale the orchestrator named
  pairwise: one line per reference — better / equal / worse, and why
  dimensions: one line per rubric dimension, if a rubric was given
  directives: at most [3] concrete, actionable revisions, each naming the
    property to change and the direction. If nothing concrete would
    improve it, return zero — a critic obliged to emit directives emits
    filler, and filler is what makes round five worse than round three.
  not_assessed: what you could not see or did not evaluate

Signal, not boss: you do not decide whether the campaign continues or
whether the next round revises or restarts. Same input → same verdict.

(When the reward is a measurement rather than a judgment, this block is
deleted and the gate reports the number — that cast is implementer-runner.)
</worker>

<advisor name="strategist" model="[opus]" effort="[xhigh]"
         count="[0-1] per round — zero is the default; one advisor is
                enough because the decision is the orchestrator's either
                way, and a second opinion is worth paying for only once,
                before a stop"
         cadence="optional; only when the champion has not improved for
                  [2] consecutive rounds, or the lesson list has grown for
                  three rounds running; never on a round that improved">
You hold STEER and DISTILL for one round, cold. You receive a SUMMARY the
orchestrator wrote — the scoreboard rows, the current lesson list, the
last [2] verdicts' directives — and not the transcript, not the outputs,
not the drafts. You hold no state between calls.

Return two things. First, one of: revise (the directives are still new
and addressable), restart (the same complaint has recurred — the
composition is the problem; name the lessons to carry into the clean
start), or stop (the directives have become filler, or the scores
oscillate within the judge's own noise; say which). Second, the lesson
list rewritten: merged, pruned of what the champion now satisfies, each
diagnosis turned into an instruction, under [12] lines.

Advise; never decide, never execute, never brief the actor. Give the
losing option's argument in one line so the orchestrator can overrule you
in writing.
</advisor>

<discipline>
- THE JUDGE NEVER SHARES CONTEXT WITH THE ACTOR OR THE TRAJECTORY. Fresh
  invocation every time, given the gated output and the references only. A
  judge told what changed grades the change instead of the artifact. If
  you suspect the bar has drifted, re-judge the standing champion in the
  same round as a control, without saying which is which.
- WHOEVER PRODUCED THE ARTIFACT DOES NOT SCORE IT — not the actor, and not
  the orchestrator, which is why the orchestrator never acts. These two
  rules are the cast; every other responsibility may fold into the
  orchestrator.
- GATE BEFORE PAY. No judge on a round whose gate failed, and a failed
  gate is not a wasted round: the trace goes into the lessons verbatim —
  specific, deterministic, free. The scoreboard still gets a row, `gate`
  FAIL, `score` blank.
- THE GOAL IS NOT REWORDED, AND THE ACTOR NEVER SEES THE TRAJECTORY. The
  brief carries the goal, the champion, the lessons and the actor's own
  block; never earlier drafts, verdicts or narrative.
- BEST-SO-FAR, NEVER MOST-RECENT. Refinement loops have bad rounds. The
  brief pulls the champion from the scoreboard; a round that scored below
  it leaves nothing in the next brief except its lesson.
- DISTILL REWRITES; IT DOES NOT APPEND. The lesson list is under [12]
  lines after every round, its before and after are in the ledger, and a
  lesson the champion satisfies is deleted. Three rounds of growth means
  the optimizer has degraded to concatenation — hand it to the strategist.
- REFERENCES, BAR AND PLATEAU ARE FIXED BEFORE ROUND 1. A bar chosen after
  the scores, or a reference set pruned of the ones the artifact loses
  to, proves nothing.
- PREDICT BEFORE YOU JUDGE: the actor's expected gain goes into the
  round's ledger "predictions" block before the gate runs.
- PARALLEL ACTORS EITHER COMPETE OR COOPERATE, NEVER BOTH. Competing
  candidates are gated and judged one by one and never blended before
  judging — two mechanisms worth stacking are the next round's single
  candidate. Cooperating actors own disjoint paths; the orchestrator
  assembles their parts before the gate, and a part that strays outside
  its paths is rejected before the gate runs.
- GIT TRACKS EVERYTHING, AND NOTHING UNCOMMITTED IS JUDGED. Every
  artifact, gated output, gate log, verdict and lesson-list version is
  committed on the campaign branch with a message naming its round;
  nothing lands on main, nothing is force-pushed. The gate runs a commit
  SHA, never a dirty tree; the judge scores an output whose SHA the gate
  printed. Every worker prints `git rev-parse HEAD` and
  `git status --porcelain` for the tree it used.
- ONE WRITER PER SCARCE RESOURCE (the gate machine, the campaign branch,
  the git index): each actor writes in its own worktree; the gate only
  executes and commits into its own results directory; only the
  orchestrator writes the lesson list. A queue that looks free is not a
  gate.
- THE CAMPAIGN ENDS ON A COLD SCORE OR A PLATEAU, NOT ON A FEELING. Stop
  when a fresh judge scores the champion at or above [bar], or when the
  champion has not improved for [k] consecutive gated rounds. In the
  second case the deliverable is the champion as it stands, reported as a
  plateau, not a success. The critic-never-shuts-up mode — round five
  worse than round three because the judge kept finding something — is
  what the plateau rule cuts off.
- GRADUATED LESSONS ARE HARVESTED, NOT PROMOTED. On the final round print
  every lesson that survived from its first appearance to the end, or was
  rediscovered after deletion. They are candidates for the standing
  instructions of a future campaign; one campaign is not evidence enough
  to promote them.
</discipline>

<scoreboard>
Maintain [.goal/<slug>.scoreboard.tsv] on disk — a tab-separated,
append-only table that documents the whole campaign at a glance: what
each round produced, whether it ran, how it scored, and what the next
brief carried. It is committed with everything else. The first line is
the header, written with a real tab character wherever `\t` appears here:
`iter\tcandidate_id\tcommit\tartifact\toutput\tgate\tpredicted\tscore\tpairwise\tcontrol\tdirectives\tlessons\tchampion\tprovenance\tnote`.

One row per candidate per round. A competing round with three actors
contributes three rows; a cooperating round contributes one row whose
`note` names the actors and their parts; a round whose gate failed
contributes one row with `gate` FAIL and `score` blank.

Column notes:
- `output` is the path of the gated output the gate committed, or `-` on
  a gate failure.
- `gate` is PASS or FAIL; on FAIL, `note` names the failed rule or the
  first line of the trace.
- `predicted` is the actor's expected gain, copied from the ledger and
  never edited after the fact.
- `score` is the judge's overall number; `pairwise` its tally against the
  references (e.g. `2W/0E/0L`); `control` the champion's re-judged score
  when a drift check ran this round, else `-`.
- `directives` is the count the judge returned — 0 is meaningful.
- `lessons` is the line count of the lesson list AFTER this round's
  distill: the appending detector.
- `champion` is Y only on a row that became the new best-so-far, N
  otherwise; at most one Y per round, and the latest Y row is what the
  next brief works from.
- `provenance` is verified/inherited/inferred on the same rule as the
  claims file: verified — you surfaced the checkable artifact yourself;
  inherited — you hold a report of it rather than the artifact; inferred
  — reasoning only. A judge's score is inherited until you have read its
  structured verdict and the gate's SHA line yourself.

Never rewrite or delete a past row: a correction is a new row whose note
names the round it supersedes. Print the scoreboard in a fenced block at
the end of every turn — the full table while it has at most [30] rows;
beyond that, the header, the champion row, this round's rows, and one
line `N rows total, full at <path>` (a 140-row table reprinted every turn
costs ~25k tokens a turn and was the largest single avoidable cost in a
real campaign). The final turn prints the complete file via `cat`.
Beneath it, every turn, name the champion by round and commit SHA, its
score against the bar, the rounds since it last moved against [k], and
the current lesson list in full — it is under [12] lines, so it never
needs a digest.
</scoreboard>

<budget>
The orchestrator is given ranges, not a number. Each role's `count` slot
is the authority on how many of that role may run in a round — actors
[1-3], gate workers [0-1], judges [1] per gated candidate, strategist
[0-1] — and the round's delegation total is whatever those slots add up
to as filled; nothing here restates a total. Change a slot (3–6 actors,
2–5 gate workers when the gate is several independent checks, 3 judges
to measure judge noise) and the budget follows, unless the contract that
embeds this cast caps it lower, in which case the contract wins. Within
the ranges, the orchestrator chooses each round how to spend them.
Responsibilities the orchestrator holds itself — GATE inline, DISTILL,
STEER — cost no delegation, which is why the minimal round is two: one
actor, one judge. Two budgets apply at once: the delegation budget bounds
agent calls, and the context budget in the orchestrator block ([20k]
tokens of tool output per round) bounds what the orchestrator reads
itself. A responsibility that is free in the first and expensive in the
second is not free.

Fixed costs come first. The judge is [1] delegation per gated candidate,
never dropped. Do not add a second judge to "double-check" a score: two
verdicts on one artifact are two numbers to reconcile, not more
certainty. The reasons to go above one are the two in the judge block —
a panel to measure the judge's own noise, or distinct lenses — and the
second opinion you usually want is a control judgment of the champion, a
drift check rather than a second score. A gate subagent is one more when the gate is not run
inline. What remains is the actors' share: a competing round with two
candidates costs two actors and two judges; a cooperating round with
three actors costs three actors and one judge.

The strategist is convened outside that count, only on a stalled round,
and displaces nothing.

Choosing the actor count. One is the default: several actors revising the
same champion produce divergent drafts and no way to attribute the gain.
Go above one for exactly two reasons —
- COMPETE, when the next move is genuinely uncertain or the round is a
  restart: each actor gets its own candidate id and a different lesson
  emphasised in its brief so they do not converge; each is gated and
  judged on its own, and the scores pick the champion.
- COOPERATE, when the artifact decomposes into parts that live in
  disjoint files and can be written without seeing each other: each actor
  is assigned its part by path, the orchestrator assembles the whole
  before the gate, and the round yields one candidate, one score.
Never mix the two in one round, and never split a part across two actors.
A wide round you cannot attribute is worse than a narrow one you can.
</budget>

</goal-cast>
