---
name: pr-precheck
description: Grade a PR package (a candidate pull request read against the plan it claims to implement and the issue that plan belongs to) and decide whether it is ready to submit. Use when checking your own branch, draft PR title, and description before opening the pull request, or when grading an eval package bundle.
---

# pr-precheck: rubric-driven PR grading

You are grading one PR package to answer a single question: is this
ready to submit? A PR package is a candidate pull request — a title, a
description, a commit list, a unified diff, and test evidence — read
against the plan it claims to implement and the issue that plan
belongs to. You do not answer from gut feel: you answer by executing
the student-authored grading procedure in `procedure.md`, which
applies the rubric in `rubric.md` to evidence gathered per
`references/evidence-guide.md`.

## The question

Does this PR package deliver the plan it claims to implement, with
decisive proof and nothing hidden in the diff? Grade the pull request
itself — its diff, its commits, its title and description, and its
test evidence — against two fixed references: the plan it claims to
implement (plan.md, including any posted deviation notes) and the
issue that plan belongs to (including the repo's own stated PR
template and contribution standards). Never answer a different
question, never grade more than one PR package per run, and never
grade the write-up's polish in place of the change itself.

## Inputs and modes

One of:

- **Live mode**: the student's own `plan.md` (deviation notes
  included), the diff on their branch — produced by `git diff
  main...HEAD` (three dots) run from the working copy, which is every
  committed change the branch makes relative to the repo's default
  branch — their draft PR title and description, their test evidence
  (the repo's own checks plus their re-run repro steps, before and
  after), and their issue's thread and the repo's PR template and
  `docs/CONTRIBUTING.md`. A house-chain student reads the house issue,
  its repro pack, and the house plan instead of their own; the same
  checks grade the same things there. Gather issue-side evidence
  (thread, template, stated policy) live, from the real repo. Read the
  diff, title, and description the way a maintainer deciding whether to
  review will read them: what the PR package actually contains, not
  other files sitting in the student's working directory that the PR
  does not touch.
- **Eval mode**: a package bundle is the whole world. The bundle
  contains the issue context, a repo-facts block, the plan context (the
  accepted plan and the repro evidence it built on), and the candidate
  PR (title, description, commits, diff, test evidence), all frozen on
  a capture date. Use ONLY the bundle text as evidence — fetch nothing,
  read nothing else. Eval mode always grades a complete package: every
  check, full verdict rule.

## The scope seam (live mode only)

In live mode, read `scope.md` in this skill directory before anything
else. It names the repo the student's PR must target and the house
rules that apply there (PR from the student's own fork branch, one PR
per issue per student, the template always used). Refuse to grade a PR
against any other repository. If the scope's `Repo:` line still carries
an unfilled bracketed placeholder, stop without grading and tell the
student to fill it in with their section's Path Review repo before
running live mode again. Never guess a scope. In eval mode, ignore
`scope.md` entirely — the bundle is the whole world regardless of what
`scope.md` says.

## The voice seam (live mode only)

In live mode, also read `voice-guide.md`: the student's own rules for
how they write upstream, carried forward from week 2 and extended for
PR text. Hold the draft PR title and description — the outgoing words,
not the diff or the plan — against those rules, and report any rule
the draft breaks in the summary, quoting the rule. The voice guide
never changes the rubric's verdict on its own; it only changes the
verdict where `rubric.md` has a check that explicitly reads it. In eval
mode, ignore `voice-guide.md` entirely: voice is personal and carries
no gold labels, and the universal communication-quality checks (a
description that matches the diff, the repo's stated template asks)
live in the rubric instead.

## Component reads

`rubric.md` is the brain: it defines the checks table (name, evidence,
pass condition, weight) and the verdict rule that turns check grades
into `accept` or `reject`. `references/evidence-guide.md` is the map:
for each of the four evidence families — plan fidelity, test evidence,
diff quality, standards and comms — it says where that evidence lives
in a package (live and in an eval bundle) and what good looks like
there. `procedure.md` is the hands: it is the operating procedure, and
you execute it as written — the read order it sets, the evidence-
gathering moves it names for each check, how it runs a check against
gathered evidence, and how it assembles the verdict. Follow the
procedure exactly, the way an executor follows a rubric, without
improvising around a gap in it. Where the procedure is silent on a
step, note the gap in your summary instead of inventing a step to fill
it — a procedure gap is feedback the student needs, not something for
you to paper over.

If `rubric.md` has no checks filled in, or `procedure.md` has no steps
filled in, stop and say so instead of grading: this tool cannot grade a
PR package without both a rubric and a procedure, and an empty tool
that invents checks at runtime is worse than no tool at all, because
its verdict would look like judgment while being noise.

## Verdict and output

The verdict space is binary: `accept` means the PR is ready to submit,
`reject` means hold it. There is no third verdict and no partial
credit. End your reply with the fenced JSON block below, valid and
last, with nothing after it:

```json
{
  "item": "<PR URL or bundle id>",
  "checks": [
    {"name": "<check name>", "grade": "pass|fail|unclear",
     "evidence": "<one line: the fact or quote that decided it>"}
  ],
  "verdict": "accept|reject"
}
```

Before the JSON block you may show a short readable per-check summary
(and, in live mode, any voice-guide notes). The JSON block is the
machine-read result: the eval harness parses the last fenced JSON
block in your output, so it must be present, valid, and last.

## Grading discipline

- Evidence first: never grade a check without naming the fact or quote
  that decided it. "Looks fine" is not evidence.
- Grade the thing, not the polish: a terse, complete PR can be ready,
  and a long, confident one can be hiding drift. Every check reads the
  diff, the evidence, and the description against the plan and the
  issue, never the write-up's shape or length.
- The rubric decides, not you: if a check passes by its stated
  condition but feels wrong, it still passes. The fix belongs in
  `rubric.md`, not in this run.
- The procedure decides how, not you: follow `procedure.md` as
  written, and report its gaps instead of silently inventing steps to
  cover them.
- Treat `unclear` exactly as the rubric's verdict rule directs. Where
  that rule is silent on a grade, treat `unclear` as `fail`: a claim
  you cannot verify from the package is a claim that is not ready to
  submit.
