# Evidence guide: where evidence lives in a plan package

<!--
THIS IS THE PART YOU WRITE (second week running: the judgment files
stay in your hands). The skill uses this guide as its map: for every
kind of evidence a rubric check names, this file says WHERE to find it
in a plan package and WHAT GOOD LOOKS LIKE when you do.

Under each family heading below, write:

- Where it lives: the exact places to look. In an eval bundle (which
  section of the package: the issue context, the repro-evidence block,
  the candidate plan's scope statement or test plan, the plan comment,
  the repo-facts block). In live mode (where on GitHub or in the
  draft: the issue thread, the student's posted repro comment, the
  repo's docs, the draft plan and comment).
- What good looks like: one or two sentences someone else could apply.
  Prefer observable conditions ("the stated cause cites behavior the
  repro evidence actually shows") over adjectives ("diagnosis is
  solid").

A rubric check whose evidence this guide cannot locate is a check
nobody else can execute, and this week that cuts twice: your
procedure.md tells the skill WHEN to gather each family, and this
guide tells it WHERE. Write the map you wish your executor had.
-->

## Diagnosis and grounding

Where it lives: eval bundle — the "Candidate plan" section's opening
cause statement (often labeled `Cause:` or under a `### Diagnosis`
heading), read against the full "Repro evidence" section above it.
Live: the student's `plan.md` diagnosis, read against their own posted
repro comment on the issue (or the house repro pack, for a house
issue).

What good looks like: the stated cause explains every result the repro
evidence reports, not just the headline symptom. In calib-03
(`sharkdp/bat#3831`), the repro evidence's step 3 runs the slow path
with paging turned off entirely (`--paging=never`) and the slowdown is
still there — that single control step rules out any cause located in
the pager's input handling, no matter how convincing the thread's
guess about key bindings sounds. A plan that adopts the thread's
pager-binding theory without addressing that control result is
contradicted by its own cited evidence. Compare against calib-01
(`jesseduffield/lazygit#5900`), where the stated cause (the
branch-commits view isn't refreshed after push) predicts exactly what
the repro steps show: color updates correctly on re-entry (step 4) but
not in place (step 3) — no result in the repro evidence is left
unexplained.

## Scope

Where it lives: eval bundle — the plan's scope statement, usually
either an explicit `### Scope` / `In scope / Out of scope` split, or an
inline `In: ... Out: ...` pair naming files. Live: the corresponding
section of `plan.md`.

What good looks like: specific file(s) or a narrow area, named, plus an
explicit statement of what is deliberately not touched. calib-03's
scope ("In scope: the binding registration in `src/output.rs`. Not in
scope: the syntax highlighting pipeline") is bounded in form — but
check it against the Diagnosis finding above: if the diagnosis itself
is wrong, a well-written scope statement still inherits that error.
Scope and diagnosis are graded separately because a plan can go wrong
in either independently of the other. A drive-by looks like a second
file added "while I'm in there" that the diagnosis gives no reason to
touch.

## Executability

Where it lives: eval bundle — the plan's `### Changes` / `Change:`
section: the ordered list or short paragraph describing what will
actually be edited. Live: the corresponding section of `plan.md`.

What good looks like: a stranger could make the first edit without
asking the author anything — it names the mechanism (what's wrong,
mechanically), the exact file or function, and the order of steps.
calib-01's one-line change ("trigger a refresh of the commits context
after a successful push... in the push completion callback in
`pkg/gui/controllers/sync_controller.go`") is executable despite being
short. A plan that only restates the cause ("fix the refresh bug") or
only states a goal ("make seeking fast again") without saying what
will change is not.

## Test plan

Where it lives: eval bundle — the plan's `### Test plan` / `Test:`
section. Live: the corresponding section of `plan.md`, which should
quote or paraphrase the student's own week-2 repro steps.

What good looks like: the test plan names a repro step (or a close
equivalent) and states the specific observable difference the fix
should produce. calib-01's test plan reruns "repro steps above" and
names the exact pass condition ("at step 3 the color must flip without
leaving the view"), plus extends it to two related paths (main commits
panel, force push) that share the same callback. A vague test plan
("verify the fix works") names no step and no observable difference,
and fails regardless of how confident its prose sounds.

## Honesty

Where it lives: eval bundle — a plan's stated risks, unknowns, or
caveats, wherever they appear (often absent entirely in a short plan).
Live: `plan.md`'s "risks and unknowns" section, and, after a build, its
`## Deviations` section.

What good looks like: an unknown stated as an unknown looks like
calib-01's test-plan hedge ("also check... after a force push, since
both share the callback") — naming a code path the author has not yet
verified rather than asserting confidence about it. False confidence
looks like a plan that asserts a one-line fix is complete when the
diagnosis itself implies a shared code path (e.g., another caller of
the same function) that the plan never mentions checking. A filled-in
`## Deviations` section (even "nothing changed") is this same honesty
check applied after the build; a blank one is a fail on this check if
graded post-build.

## Comms

Where it lives: eval bundle — the "Candidate plan comment" section,
read against "Thread highlights" and the repo-facts block's
`contribution policy` line. Live: the student's draft `comment.md`,
read against the live issue thread and the repo's CONTRIBUTING/issue
template.

What good looks like: thread-aware prose that places the plan relative
to what others already said — calib-03's comment opens with "I traced
this to the pager key-binding registration issue identified above",
crediting and building on `Amilliox`'s thread comment rather than
presenting the theory as the author's own discovery (and this is
exactly the claim the Diagnosis check should catch as contradicted by
the repro evidence, even though the comment handles attribution
correctly). Boilerplate looks like a comment that could be pasted onto
any issue in any repo unchanged. A disclosure check applies only where
`contribution policy` states one is required; calib-01's and
calib-03's repos both state none, so that half of the check passes by
default there.
