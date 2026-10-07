# Procedure: how this skill grades a plan package

<!--
THIS IS THE PART YOU WRITE, and it is a new kind of part. Weeks 1 and
2, SKILL.md carried a numbered workflow and you only wrote judgment
files. This week the workflow is gone from the frame: SKILL.md says
"execute procedure.md", and these are the operating steps you author.
The machinery is in your hands now.

Your operator swap is the design brief. When your executor stalled
because your rubric said WHAT to decide but not HOW to find the
evidence, that was a procedure gap. This file is where those gaps get
closed: a complete procedure lets someone who has never seen a plan
package before (a groupmate, or the skill itself) grade one exactly the
way you would.

Under each stage heading below, write the concrete steps for that
stage. The one-line note under each heading says what a complete
procedure must decide there. Write steps, not intentions: "read the
repro evidence before the plan, and note what behavior it pins down"
is a step; "understand the context" is a wish.
-->

## Read order

1. Read the issue body first (title, repro steps as the reporter told
   them, expected vs. actual). Note the behavior the reporter describes
   in one sentence.
2. Read the thread highlights next, in date order. Note any comment
   that proposes a cause or a fix, and who posted it (a maintainer's
   claim carries differently than an unconfirmed guess from another
   reporter). This is read before the repro evidence because a plan
   that merely restates a thread guess, without the repro evidence
   confirming or ruling it out, is a different failure than a plan that
   ignores the thread entirely.
3. Read the repro evidence block in full, step by step. Note every
   distinct result it reports, including any step that was run as a
   control (e.g., with a suspected component disabled, or removed from
   the loop entirely) — a control step is often what rules a candidate
   cause in or out, and it is the single most commonly overlooked piece
   of evidence.
4. Read the candidate plan in full (cause, scope, changes, test plan,
   risks/unknowns, in that order if the plan has sections).
5. Read the candidate plan comment last, as a stranger on the issue
   thread would read it: on its own, without the plan file open beside
   it.
6. Read the repo-facts block (contribution conventions, AI-disclosure
   policy, bug-report template) once, and keep it in mind for the
   Comment-matches check; it does not bear on diagnosis or scope.

## Evidence gathering

- **Diagnosis grounded in evidence**: list every result the repro
  evidence reports (step 3 above). For each one, ask whether the
  plan's stated cause predicts it. Pay special attention to a control
  result (a step that isolates one variable) — a cause that the
  control result contradicts is a fail regardless of how well the rest
  of the plan reads.
- **Scope bounded**: pull the plan's named file(s)/area(s) and its
  stated out-of-scope line. Compare the named files against what the
  diagnosis actually implicates — a file named in scope that the
  diagnosis never mentions is scope creep.
- **Executable by a stranger**: pull the plan's changes/approach
  section. Check it answers, in order: what mechanism changes, in
  which file(s), and what the first concrete edit is. If any of the
  three is missing, note which one.
- **Test plan decisive**: pull the plan's test plan and the repro
  evidence's steps side by side. Check the test plan names at least
  one of the repro steps (or a direct equivalent) and states what
  result after the fix would differ from the actual result recorded in
  the repro evidence.
- **Honest about unknowns**: pull the plan's risks/unknowns statement
  (or note its absence). Compare it against the diagnosis and repro
  evidence for any open question the plan's own confident language
  does not mention (e.g., a shared code path the fix touches that the
  repro evidence never exercised).
- **Comment matches thread and repo**: pull the plan comment's stated
  cause/approach and compare it against the thread highlights gathered
  in read order step 2. Separately, check the plan comment (not the
  plan file) for an AI-assistance disclosure if the repo-facts block's
  contribution policy states one is required.

## Check execution

1. Grade checks in the table's row order. Each check is graded once,
   from the evidence already gathered above; do not re-read the whole
   package per check.
2. Grade `pass` only when the gathered evidence affirmatively meets the
   pass condition. Grade `fail` when the gathered evidence contradicts
   the pass condition or a required element is stated but wrong.
   Grade `unclear` only when the evidence needed is genuinely absent
   from the package (not merely terse) — for example, no repro
   evidence block at all, or a plan with no scope statement of any
   kind.
3. If the plan comment and the plan file disagree on the stated cause
   or scope, grade both the plan-facing checks (diagnosis, scope,
   executability, test plan) from the plan file, and grade "Comment
   matches thread and repo" from the comment itself; note the
   disagreement in that check's evidence line regardless of its grade.
4. A `preferred` check (Honest about unknowns) is still graded with
   the same rigor as a required one; it simply does not gate the
   verdict in step 1 below.

## Verdict assembly

1. Apply the rubric's verdict rule: accept if every `required` check
   graded `pass`; reject if any `required` check graded `fail` or
   `unclear`. The `preferred` check is reported but never flips this.
2. When rejecting, the evidence line for the first `required` check
   (in table order) that failed or was unclear is the one quoted in
   the output summary as the deciding check — do not quote a
   `preferred` check as the reason for a reject.
3. The verdict is binary (`accept`/`reject`); there is no partial
   credit and no averaging of check grades.
