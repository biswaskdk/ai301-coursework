# Unit 4 — Test and Submit

Path: `beat-1-sandbox/unit-4/pull-request.md`

Record of the pull request you opened against the Path Review repo, and of the evaluation
runs that produced `eval-run.txt`. This file is graded at the path above; a copy kept
anywhere else in the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Your pull request

**Pull request**

https://github.com/codepath/pathreview-ai301-fa26-s3/pull/130

**Branch**

`fix/68-empty-keyword-index`

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

1. Full run (first pass): `agreement: 16/20 scored items  (bar: 18/20: below the bar)`.
   Four clear-accept packages (pkg-05, pkg-11, pkg-13, pkg-19) were wrongly rejected,
   three on "Test evidence is decisive" and one on "Description matches the diff" —
   both checks were written too strictly (demanding a separately pasted transcript for
   every named control step, and a literal per-item count for bundled description
   claims).
2. `--only pkg-05,pkg-11,pkg-13,pkg-19,pkg-04,pkg-10,pkg-03,pkg-09` (after loosening
   both checks' pass conditions, with pkg-04/pkg-10/pkg-03/pkg-09 as canaries from the
   not-tested and silent-drift categories the change could flip): `agreement: 8/8
   scored items`, no canary flipped.
3. Full run with `--save-run eval-run.txt` (final, committed): `agreement: 20/20 scored
   items  (bar: 18/20: PASS)`.

**Package analysis**

`pkg-05` (nushell/nushell#18848): gold label `accept` (clear-accept). My rubric's first
full run graded it `reject`, failing "Test evidence is decisive," because the evidence
states the plan's second test-plan step (two same-name, same-key bindings producing one
row plus a warning) only through an added unit test's assertions, not a separately
pasted before/after transcript like the issue's own repro got. My pass condition at the
time effectively required a shown transcript for every test-plan step, which this
package's otherwise-decisive evidence didn't give for that one step. After loosening the
check to accept a specific, named claim or an equivalent automated-test assertion for a
secondary step (not just the primary repro), re-grading this package flipped it to
`accept`, matching gold.

**Check rationale**

From `tools/pr-precheck/rubric.md`, **Diff matches plan scope**:

> Pass if every changed file and every hunk falls inside the plan's stated in-scope area, or is named in a deviation note written into the plan before/alongside the PR. Fail if the diff touches a file or behavior the plan scopes out or never mentions, and no deviation note accounts for it — regardless of whether the extra change looks adjacent or beneficial. Unclear if the package has no plan scope statement to compare against

This makes scope fidelity depend on the actual changed files and hunks, while allowing a documented deviation to account for a changed approach. It is intended to catch silent drift without rejecting an honestly disclosed shortfall.

**Trade-offs**

The required checks deliberately treat an `unclear` result as a failure, so an unverifiable
claim can hold an otherwise plausible PR. The trade-off is stricter review when evidence is
missing; the rubric avoids penalizing a disclosed deviation when the other required checks
pass.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/pr-precheck/`.
