# Procedure: how this tool grades a PR package

## Read order

1. Read the issue first (title, body, labels), then the thread
   highlights, noting any maintainer comment that bears on expected
   scope or standards (e.g., a maintainer already rejecting a
   particular approach).
2. Read the repo-facts block (or, live, the PR template and
   `docs/CONTRIBUTING.md`) next, once, and hold its stated sections and
   policy in mind for the Repo-standards check later. Reading it before
   the plan and diff means a template ask ("remove the xfail marker")
   is recognized as a standards question, not mistaken for scope creep
   when it shows up in the diff.
3. Read the plan context in full: repro evidence, then the plan's
   Scope (in/not-in), Approach, Test plan, and Risks/Deviations, in
   that order. Note the named in-scope files/areas and the stated test
   plan's specific repro steps — these are the two things every
   side-by-side check below pairs against.
4. Read the diff and commit list in full, hunk by hunk, before reading
   the description. Reading the diff before the description is
   deliberate: a description read first anchors the eye on its own
   claims, which is exactly how an inflated description gets missed
   against a diff that does less (or more) than it says.
5. Read the description and checklist last, as a maintainer deciding
   whether to review would: on its own, holding its claims against
   what was just read in the diff.
6. Read the test-evidence section (or `test_evidence.md`) alongside the
   plan's test plan and the original repro evidence's steps.

## Evidence gathering

- **Diff matches plan scope**: list every file and distinct hunk the
  diff touches (from step 4). For each, check it against the plan's
  named in-scope area (step 3) or the Deviations note. Flag anything
  not covered by either, no matter how small or adjacent-looking (a new
  flag, a renamed helper, a reworded unrelated string).
- **Description matches the diff**: list every factual claim the
  description makes about what changed (step 5). For each, confirm the
  diff (step 4) actually contains it. Separately, check whether the
  plan called for anything the diff does not deliver, and whether the
  description discloses that gap or stays silent on it.
- **Test evidence is decisive**: pull the plan's test plan steps and
  the original repro evidence's steps (step 3) and the package's
  test-evidence section (step 6) side by side. Check each plan step is
  re-run and shown with an observable before/after, that any named
  control step is re-run and shown unchanged, and that the repo's
  required checks are reported with their actual outcome.
- **Diff is reviewable**: scan every hunk from step 4 for debug
  artifacts, commented-out code, dead code, formatting-only hunks, and
  unrelated edits; scan the commit list for process-note-only messages.
- **Repo standards met**: pull the repo-facts block's or template's
  required sections and policy (step 2) and check each one against the
  description and checklist (step 5): present, filled with real
  content, `Closes #` correct, checklist boxes backed by the diff,
  disclosure present where required.
- **Honest about unknowns**: pull the plan's Risks/Deviations (step 3)
  and compare against the description's confidence level (step 5) for
  any open item the description's language glosses over.

## Check execution

1. Grade checks in the table's row order, each once, from the evidence
   already gathered above — do not re-read the whole package per check.
2. Grade `pass` only when gathered evidence affirmatively meets the
   pass condition. Grade `fail` when gathered evidence contradicts the
   pass condition, however minor the violation (one leftover debug
   line is still a fail on Diff is reviewable). Grade `unclear` only
   when the evidence needed is genuinely absent from the package (no
   test-evidence section at all, no scope statement at all) — not
   merely terse.
3. If the description and the diff disagree, grade "Diff matches plan
   scope" from the diff against the plan, and grade "Description
   matches the diff" from the description against the diff; note the
   disagreement's direction (more than the plan, or less than claimed)
   in that check's evidence line.
4. Apply the Repo-standards-met exception exactly as the rubric states
   it: `pass` (not `unclear`) only when the package truly states no
   template and no policy to check against; in live mode this never
   applies.
5. The `preferred` check (Honest about unknowns) is graded with the
   same rigor as the required ones; it is reported but, per step 1
   below, never gates the verdict.

## Verdict assembly

1. Apply the rubric's verdict rule: `accept` if every `required` check
   graded `pass`; `reject` if any `required` check graded `fail` or
   (outside the stated exception) `unclear`. The `preferred` check never
   flips this.
2. When rejecting, quote the first `required` check (in table order)
   that failed or was unclear as the deciding check in the output
   summary — never quote the `preferred` check as the reason for a
   reject.
3. The verdict is binary (`accept`/`reject`); there is no partial
   credit and no averaging of check grades.
