# Evidence guide: where proof lives in a reproduction package

<!--
THIS IS THE PART YOU WRITE (new this week: Unit 1 handed you this file
finished; the scaffolding fades). The skill uses this guide as its map:
for every kind of proof a rubric check names, this file says WHERE to
find it in a package and WHAT GOOD LOOKS LIKE when you do.

Under each family heading below, write:

- Where it lives: the exact places to look. In an eval bundle (which
  section of the package: the issue context, the repo-facts block, the
  claim comment, the repro report and its parts). In live mode (where
  on GitHub or in the draft: the issue thread, the repo's docs, the
  student's draft comment).
- What good looks like: one or two sentences someone else could apply.
  Prefer observable conditions ("the versions named match what the
  issue targets, or the difference is called out") over adjectives
  ("environment is thorough").

A rubric check whose evidence this guide cannot locate is a check
nobody else can execute; the rubric swap showed you what that feels
like. Write the map you wish your grader had.
-->

## Environment

Where it lives: in an eval bundle, the opening line(s) of
`repro_report_markdown` (versions of the runtime, package, and OS), read
against `issue.body_markdown` and `repo_facts.raw_markdown` (the
"latest release" line and any version the reporter names). In live mode,
the environment line(s) at the top of the repro report draft, read
against the version(s) named in the issue thread and the repo's current
release.

What good looks like: every version the issue's own text depends on
(the package/library version, the language runtime, the OS where it
matters) is named explicitly in the report, not implied ("current
version") or left to be inferred from a screenshot. If the reporter's
version and the recorded version differ, the report says so in words
("reproduced on 3.2.4; issue was filed against 3.2.3") rather than
letting the reader notice the gap. Missing entirely, or vague enough
that a stranger couldn't tell if they're on the same footing, is a
fail.

## Steps

Where it lives: the numbered or sequential steps in
`repro_report_markdown`, between the environment line and the
output/artifact block. In live mode, the same section of the draft
repro report file.

What good looks like: each step is a command, action, or input a
stranger could type or click without first guessing at missing state —
no step assumes a file, config, login, or prior step that isn't itself
written down earlier in the same report. The steps start from a named
state (a fresh clone, a specific branch, `--offline` mode, etc.) and
end at the point where the behavior appears. A step like "set up the
project" or "run the usual tests" with no commands is a fail; a step
that silently depends on an earlier unstated setup (a seeded database,
an env var never mentioned) is a fail even if the commands shown are
correct.

## Behavior shown

Where it lives: the output/log block(s) at the end of
`repro_report_markdown` (what actually ran and printed), read against
`issue.body_markdown`'s own description of the bug and, when present,
`thread_highlights` (later commenters narrowing or confirming the
symptom). In live mode, the same output block in the draft, read
against the issue thread on GitHub.

What good looks like: the shown output contains the same symptom the
issue names — the same error text, the same wrong value, the same
missing field — not a symptom that merely happens nearby (a different
error, a different code path, a passing case dressed up as the failing
one). A control/contrast run (showing the correct behavior alongside
the broken one, as in "with zero custom headers it is set correctly")
strengthens this but isn't required. An artifact that's present but
too cropped or generic to tell whether it matches ("it broke, see
attached") is unclear, not a pass.

## Honesty

Where it lives: the concluding sentence(s) of `repro_report_markdown`
(and, on a claim-only package, the forward-looking sentence in
`claim_comment_markdown`), read against the artifact block directly
above it in the same report. In live mode, the closing line of the
draft repro report, read against its own output block.

What good looks like: the stated conclusion's strength matches what
the artifact actually shows — "reproduced" only appears when the
output block shows the issue's exact behavior; "could not reproduce"
is stated plainly, with the attempted steps and environment shown,
when the behavior didn't appear. A claim comment promises
investigation ("I can reproduce X, next I want to check Y") rather
than asserting a fix or a diagnosis it hasn't earned yet. A report that
says "reproduced" over an artifact showing something adjacent, or that
hedges with more confidence than the evidence supports, is a fail.

## Comms

Where it lives: `claim_comment_markdown` and `repro_report_markdown`'s
own prose (not its code blocks), read against
`repo_facts.raw_markdown`'s bug-report-template and AI-disclosure
lines. In live mode, the issue's template/CONTRIBUTING.md on GitHub and
the repo's stated AI-assistance policy, read against the draft
comment's actual text.

What good looks like: the comment answers what the repo's own template
asks for (e.g., confirming the reporter searched for duplicates, naming
a version, giving minimal repro steps) using specifics from this
issue, not boilerplate that could be pasted onto any issue. If
`repo_facts` or the repo's docs state an AI-assistance disclosure
requirement, the comment discloses it plainly. A comment that skips a
stated template requirement, or omits a disclosure the repo's policy
requires, is a fail regardless of how well-written it otherwise reads.
