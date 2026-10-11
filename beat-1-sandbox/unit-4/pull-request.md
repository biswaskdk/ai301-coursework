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

Not opened yet.

**Branch**

`fix/68-empty-keyword-index`

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

No `pr-precheck` evaluation run has been performed. The `20/20` run recorded under
Unit 3 is for the different `plan-check` skill and is not a valid Unit 4 result.

**Package analysis**

Not available yet: no scored package has been graded with `pr-precheck`, so there is no
package verdict to compare with its gold label.

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
