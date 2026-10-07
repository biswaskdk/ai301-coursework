# Rubric: is this plan ready to post and build from?

<!--
THIS IS THE PART YOU WRITE. The skill in SKILL.md executes whatever
checks you define here (via your procedure.md). It ships empty on
purpose: the judgment is your work.

A filled rubric must contain:

1. At least one row in the checks table. Each row needs all four
   columns:
   - Check: a short name (used in the output JSON).
   - Evidence: exactly what to look at, and where in the package. Name
     the part (the plan's scope statement, the test plan read against
     the repro evidence's steps, the plan comment read against the
     thread highlights, the repo-facts block) or a location from your
     references/evidence-guide.md. "The plan" is not a source; "the
     plan's stated cause read against what the repro evidence shows"
     is.
   - Pass condition: a decision rule about the OUTCOME that someone
     else could apply and get your answer. Judge the thing itself (is
     this one bounded change? could a stranger start executing it?),
     never the write-up's shape (how many sections it has, how long it
     is, whether it uses headings). Structure-shaped checks are what
     make graders disagree with themselves.
   - Weight: `required` (a fail here holds the package) or `preferred`
     (never changes the verdict).

2. A verdict rule below the table: how the check grades combine into
   accept (ready) or reject (hold), including how `unclear` is
   treated. The verdict space is binary. If you write no rule for
   `unclear`, the skill treats it as fail.

Cover what actually gets bad plans posted. The lecture named the
failure families: the diagnosis ignores or contradicts the reproduced
evidence, the change is unbounded (scope creep), the plan targets the
symptom while the evidence points at the cause, a stranger could not
start executing it, the test plan proves nothing observable, the
unknowns are dressed up as certainty, and the comment ignores what the
thread or the repo's stated conventions ask. A rubric that ignores a
family will fail eval packages designed around that family.
-->

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| Diagnosis grounded in evidence | The plan's stated cause, read against everything the repro evidence block actually shows (not just what the issue thread speculates) | Pass if the stated cause accounts for every result in the repro evidence, including any result that rules out a competing explanation (e.g., the behavior still occurring with a suspected component disabled or removed from the loop). Fail if any part of the repro evidence contradicts the stated cause, or the cause explains only part of what was observed. Unclear if the repro evidence is missing or too thin to confirm or rule out the stated cause | required |
| Scope bounded | The plan's scope/in-out statement and the files or areas it names | Pass if the plan names the specific file(s) or area(s) it will change and states what it will not touch, and that named scope is no broader than the diagnosis requires. Fail if either the in-scope or out-of-scope half is missing, or the plan reaches into files/areas the diagnosis gives no reason to touch | required |
| Executable by a stranger | The plan's approach/changes section: what will be done, where, in what order | Pass if someone who has not talked to the author could start making the first change without asking a clarifying question — the mechanism, the file(s), and the order of steps are all stated. Fail if the plan states only the goal or the cause without saying what will actually be changed, or skips a step a stranger would need | required |
| Test plan decisive | The plan's test plan, read against the repro evidence's steps and artifacts | Pass if the test plan re-runs (or directly extends) the repro evidence's steps and states an observable result after the fix that would differ from the observed bug. Fail if the test plan is vague ("verify it works"), omits an automated or repeatable check entirely, or does not tie back to the repro steps | required |
| Honest about unknowns | The plan's risks/unknowns statement, read against how confidently the rest of the plan is written | Pass if genuine uncertainty (an untested edge case, an assumption about a shared code path, a dependency on another fix) is named as such. Fail if the plan asserts a fix with total confidence while the repro evidence or the diagnosis itself leaves an open question unaddressed and unmentioned. Unclear only if there is no way to tell whether an unknown exists (rare; most plans surface at least one risk if asked) | preferred |
| Comment matches thread and repo | The plan comment, read against the thread highlights (has anyone already proposed a cause or fix?) and the repo-facts block's stated contribution/AI-disclosure conventions | Pass if the comment acknowledges a relevant prior thread claim it agrees with or departs from (not silence when the thread already named a cause), and discloses AI assistance where the repo's stated policy requires it. Pass by default if the thread has no highlights and the repo states no disclosure policy. Fail if the comment ignores a thread claim that bears directly on its own diagnosis, or omits a disclosure the repo's stated policy requires | required |

## Verdict rule

Accept if every `required` check grades `pass`. Reject if any
`required` check grades `fail` or `unclear`. `preferred` checks
(currently: Honest about unknowns) never change the verdict; they are
reported but do not gate accept/reject. `unclear` on a required check
is treated as `fail`: a cause, scope, or test plan I cannot verify from
the package is not ready to build from.
