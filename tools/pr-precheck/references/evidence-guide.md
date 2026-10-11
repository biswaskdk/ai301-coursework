# Evidence guide: where evidence lives in a PR package

## Plan fidelity (harness category: silent-drift)

Where it lives:
- **Eval bundle**: the plan-context block's scope statement ("In
  scope" / "Not in scope"), approach, and any deviation note, read
  against the candidate PR's diff and commit list. The description's
  own fidelity claims ("implements the plan exactly", "no changes
  beyond it") sit in the Description section.
- **Live mode**: `plan.md`'s Scope and Approach sections plus its
  Deviations section (an honest deviation re-ties a mismatch), read
  against `git diff main...HEAD` and the draft `pr_draft.md`
  description's own claims about what it changed.

What good looks like: every file the diff touches falls inside the
plan's named in-scope area, or is accounted for in a deviation note
written before the PR was drafted. The description's claims about what
it does match the diff exactly — neither more (an unannounced feature,
an extra flag, a drive-by rename) nor less (a claimed doc update that
never lands in the diff). Silent drift runs in both directions: a diff
that quietly does *more* than the plan (scope creep with no note) and a
description that quietly claims *more* than the diff delivers (fidelity
claimed, not shown) are the same failure. A deviation recorded in
`plan.md`/the plan context, stated plainly and reflected accurately in
the description, is not drift — it is honest work, and keeps this
check passing even when it changes what got built.

## Test evidence (harness category: not-tested)

Where it lives:
- **Eval bundle**: the candidate PR's Test evidence section, read
  against the plan context's repro evidence and stated test plan.
- **Live mode**: `test_evidence.md` (the before/after re-run of the
  Unit 2 repro steps, plus the output of the repo's own checks: `make
  test-unit`, `make test-integration`, `make lint`, `make typecheck`),
  read against `plan.md`'s Test plan section and the original repro
  evidence.

What good looks like: the evidence names a specific, observable
behavior from the plan's repro steps and shows it before (the bug) and
after (the fix) — not "tested locally, works now" with nothing named.
A control step the plan named is accounted for with a specific,
concrete statement of its result ("the no-ampersand control
unchanged", "the all-comments control unchanged") — it does not need
its own pasted transcript, but a silent drop of a control the plan
explicitly named is not decisive. The repo's own checks are actually run and their real outcome is pasted, including a
check that fails or cannot run (Path Review currently has no
integration tests, so `make test-integration` reporting "no tests ran"
is expected and still counts as evidence when reported honestly) —
hiding a check's output, or claiming "tests pass" without showing which
suite ran, is the harness's not-tested failure. Evidence that exercises
only an unrelated, already-working path (a control case, or a scenario
the fix didn't touch) while leaving the plan's actual repro step unrun
is not decisive, even if something was run.

## Diff quality (harness category: unreviewable)

Where it lives:
- **Eval bundle**: the candidate PR's unified Diff and Commits
  sections.
- **Live mode**: `git diff main...HEAD` and `git log main..HEAD` (or
  the branch's commit list) in the working copy.

What good looks like: the diff's every hunk is part of the fix the plan
describes; a reviewer can read start to finish and see only the
change, nothing riding along. Debris that fails this check: debug
print/log lines left in, commented-out code (an old attempt, a dead
experiment), a dead function kept behind a lint-suppressing decorator
or comment, formatting-only churn mixed into the real hunks, an
unrelated rename or cleanup the plan never scoped in, and commit
messages that read as process notes ("wip", "fix", "fmt + cleanup")
rather than describing the change. `git diff main...HEAD --stat` run
right before pushing is the live-mode check that the diff contains only
the files the plan (plus any deviation note) actually touches.

## Standards and comms (harness category: standards-wall)

Where it lives:
- **Eval bundle**: the repo-facts block's stated PR-template sections,
  contribution policy, and AI-use disclosure policy (if any), read
  against the candidate PR's Description, Checklist, and Commits.
- **Live mode**: the Path Review repo's `.github/PULL_REQUEST_TEMPLATE.md`
  and `docs/CONTRIBUTING.md`, read against the draft `pr_draft.md` and
  the course's own standing ask that every PR's Notes-for-Reviewers
  section disclose how AI was used on the change (the template has no
  dedicated section for this, so it is graded wherever the description
  places it).

What good looks like: every section the repo's template requires is
present and filled with real, PR-specific content — not a restated
heading or a placeholder comment left in place. Path Review's template
asks, specifically: Summary, `Closes #<issue>` under Issue, a bullet
per change under Changes, only-true boxes ticked under Testing, with
before/after and the four checks' results pasted in (the CI box stays
unticked, since CI only runs after the PR opens and is not part of this
check), a Screenshots/Demo entry or an explicit "N/A" with a reason,
and Notes for Reviewers covering both the AI-use disclosure and any
planned deviation from `plan.md`. A seeded-bug fix additionally ticks
the template's "removed its `@pytest.mark.xfail` marker" box only when
that marker (and any matching `pyproject.toml` suppression) is actually
gone from the diff. A section left as the template's own placeholder
comment, a missing `Closes #`, or a disclosure the repo's stated policy
requires but the description never gives is a standards-wall failure,
regardless of how good the diff itself is. (Whether the description's
claims match the diff's actual content is plan fidelity, above — this
family is about the repo's own stated asks, not internal consistency.)
