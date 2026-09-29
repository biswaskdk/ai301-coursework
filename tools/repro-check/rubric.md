# Rubric: is this reproduction package ready to post?

<!--
THIS IS THE PART YOU WRITE. The skill in SKILL.md executes whatever
checks you define here. It ships empty on purpose: the judgment is your
work.

A filled rubric must contain:

1. At least one row in the checks table. Each row needs all four
   columns:
   - Check: a short name (used in the output JSON).
   - Evidence: exactly what to look at, and where in the package. Name
     the part (the claim comment, the repro report's environment
     record, the artifacts read against the issue's description, the
     repo-facts block) or a location from your
     references/evidence-guide.md. "The report" is not a source; "the
     output excerpt read against the error the issue describes" is.
   - Pass condition: a decision rule about the OUTCOME that someone
     else could apply and get your answer. Judge the thing itself (does
     the artifact show the issue's behavior?), never the write-up's
     shape (how many steps it has, how long it is, whether it uses a
     template's headings). Structure-shaped checks are what make
     graders disagree with themselves.
   - Weight: `required` (a fail here holds the package) or `preferred`
     (never changes the verdict).

2. A verdict rule below the table: how the check grades combine into
   accept (ready) or reject (hold), including how `unclear` is
   treated. The verdict space is binary. If you write no rule for
   `unclear`, the skill treats it as fail.

Cover what actually gets bad packages posted. The lecture named the
proof families: the environment is recorded, the steps are complete
and followable, the behavior shown matches the issue (not an adjacent
one), the outcome is stated honestly (an evidenced cannot-reproduce is
a pass, a confident wrong-target is not), and the words respect the
repo's conventions. A rubric that ignores a family will fail eval
packages designed around that family.
-->

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| Environment recorded | The repro report's environment record (OS/runtime/dependency versions, or the repo-facts block in eval mode), compared against the version(s) the issue's own text targets | Pass if the recorded versions match what the issue targets, or a mismatch is explicitly called out as a difference; fail if a version the issue depends on is missing from the record or silently differs from it; unclear only if the issue names no target version to compare against | required |
| Steps followable | The reproduction steps in the repro report, read start to trigger | Pass if someone with only the recorded environment could execute the steps as written, in order, from a stated starting state, and reach the same trigger point with no assumed or missing setup; fail if any step depends on information, state, or context not written down | required |
| Behavior matches issue | The output/log/screenshot artifact(s) in the repro report, read against the behavior the issue's own text describes | Pass if the artifact shows the same behavior the issue describes (same error, same symptom, same failure point); ALSO pass if the report's stated conclusion is an honest cannot-reproduce (see Outcome check) and the artifact shows the attempted steps ran to completion without producing the issue's behavior, with any environment difference from the issue called out — showing no matching behavior is the correct outcome there, not a gap. Fail if the report claims to reproduce the issue but the artifact shows a different or merely adjacent behavior. Unclear if an artifact is present but too partial to compare against what the issue describes, under either conclusion | required |
| Outcome stated honestly | The report's stated conclusion (reproduced / could not reproduce), compared against what the gathered artifacts actually support | Pass if the stated conclusion is exactly as strong as the evidence shown — an evidenced cannot-reproduce, reported as such, is a pass; fail if the report asserts reproduction the artifacts don't back, or states more confidence than the evidence supports | required |
| Repo conventions and disclosure | The claim and repro comments, read against whichever part of the repo's stated conventions actually applies to a contributor's comment at that stage (an explicit AI-assistance disclosure policy, or a contribution guide's stated claim/PR-comment expectations) — NOT a bug-report template aimed at people filing a new issue, which does not apply to a comment on an issue someone else already filed (repo-facts block in eval mode; the repo's CONTRIBUTING docs or issue template in live mode) | Pass if the comments disclose AI assistance where the repo's policy requires it, and meet any contribution-guide expectation the repo states for a claim/repro comment specifically; pass by default if the repo's stated conventions address only bug-report filing and say nothing about claim/repro comments. Fail if a disclosure the repo requires is missing, or a stated claim/repro-comment expectation is ignored | required |

## Verdict rule

Accept if every required check grades `pass`. Reject if any required
check grades `fail` or `unclear`. There are no `preferred` checks in
this rubric, so none apply here; if one is added later, it never
changes the verdict on its own. `unclear` on a required check is
treated as `fail`: proof I cannot verify is proof that is not ready to
post. (Claim-only checks that the skill has already excluded per
SKILL.md's claim-only rule are not part of this rule.)
