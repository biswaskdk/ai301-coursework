# Unit 1 — Issue Selection

Path: `beat-1-sandbox/unit-1/selection.md`

Record of the issue carried into Unit 2, and of the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in
the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the
wrong label is not graded.

---

## Selected issue

**Issue link**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/68

**Verdict output**

```
## Summary

**Repo-level (applies to all three):** `codepath/pathreview-ai301-fa26-s3` is not archived, last push 2026-09-16 (4 days old), 5 recent human commits (Andrew Burke/Aburke225) — maintainer alive and repo in active use. `docs/CONTRIBUTING.md` states no AI ban (silence passes). No PRs exist anywhere in the repo.

**Ranked read-out (fit profile: backend/API Python, wants real bug-fixing incl. error handling/input validation, wants to avoid doc/frontend work):**

1. **#68 — `ZeroDivisionError` on empty keyword-search index** → **accept**. Best fit: a concrete backend Python error-handling bug (unhandled exception on empty input, exactly the "error handling, input validation" the profile wants). A classmate posted an intent-to-work comment, but the Path Review house rule says claim comments don't block — claim anyway.
2. **#54 — resume section detection fails on leading whitespace** → **accept**. Also a solid backend Python bug with a runnable repro, but it's regex/parsing logic rather than error-handling specifically.
3. **#73 — README/.env.example key-name mismatch** → **accept**, but ranks last: it's a pure documentation-consistency fix, which the fit profile explicitly said to avoid.

All three pass every required check (none archived/unassigned/claimed-blocking/unbounded/banned), so all are viable; ranking is fit only.

{
  "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/68",
  "checks": [
    {"name": "not-archived", "grade": "pass", "evidence": "repo API: \"archived\": false"},
    {"name": "maintainer-alive", "grade": "pass", "evidence": "human commit by Andrew Burke (Aburke225) on 2026-09-16, within 90 days"},
    {"name": "repo-in-use", "grade": "pass", "evidence": "pushed_at 2026-09-16, within 90 days of today (2026-09-20)"},
    {"name": "bounded-scope", "grade": "pass", "evidence": "single fix in rag/retriever/keyword_search.py plus removing one xfail marker; no debate in thread"},
    {"name": "unassigned", "grade": "pass", "evidence": "\"assignees\": []"},
    {"name": "no-active-claim", "grade": "pass", "evidence": "classmate claim comment from acordero4852 (NONE) on 2026-09-19; house rule: student claim comments do not block, claim anyway"},
    {"name": "ai-policy-permits", "grade": "pass", "evidence": "docs/CONTRIBUTING.md has no AI restriction; silence passes"},
    {"name": "newcomer-label", "grade": "pass", "evidence": "labels include \"good first issue\""},
    {"name": "maintainer-backed", "grade": "pass", "evidence": "opened by Aburke225, author_association COLLABORATOR"},
    {"name": "clear-spec", "grade": "pass", "evidence": "body states actual behavior (raises ZeroDivisionError) vs expected (should return empty like search())"},
    {"name": "fresh-history", "grade": "pass", "evidence": "opened 2026-09-10, no closed-unmerged PRs (zero PRs found)"},
    {"name": "adoption", "grade": "fail", "evidence": "stargazers_count: 2"}
  ],
  "verdict": "accept"
}
```

**The verdict must record `accept` for this issue.** Choose an issue your own skill
accepts. If your skill rejects every candidate you try, that is a signal about your
rubric rather than about the issues: revise it and re-run — retries are unlimited and a
partial re-run costs about $0.20 — or run the skill on different candidates. Output
recording `reject` for the issue you chose earns no credit for this field.

---

## Eval iterations

**Run history**

1. `--only issue-01` (after the first bounded-scope edit): `agreement: 1/1 scored items`
2. Full run: `agreement: 17/20 scored items  (bar: 18/20: below the bar)`
3. `--only issue-01,issue-04,issue-09,issue-19,issue-20` (after two more bounded-scope edits): `agreement: 5/5 scored items`
4. Full run: `agreement: 19/20 scored items  (bar: 18/20: PASS)`
5. Full run with `--save-run eval-run.txt` (final, committed): `agreement: 20/20 scored items  (bar: 18/20: PASS)`

**Issue analysis**

`issue-01`. My rubric's decision: `accept`. Gold label: `accept`. Reasoning: this issue
(conda/conda#16475) is a docs task with a detailed "Proposed changes" section listing a
new page plus edits to three existing pages. My rubric's `bounded-scope` check originally
read that list as an umbrella issue (a checklist of independent sub-items meant for
separate PRs) and failed it, producing a `reject` that disagreed with gold. I revised the
check to say a detailed plan is not a fail when it describes ONE deliverable that
enumerates the specific files or sections to touch along the way — that's a single
bounded change (one PR), distinct from an umbrella whose sub-items are independent work
for separate contributors. Under the revised wording the issue reads as one docs
deliverable (the new page plus its cross-references), so the check passes and the
verdict flips to `accept`, matching gold.

**Check rationale**

`bounded-scope`, quoted as currently written in `rubric.md`:

> Pass if the issue asks for one concrete, bounded change (a specific bug, a specific
> doc or test fix, one well-defined feature slice). FAIL if any of: it is an umbrella or
> tracking issue (a list of sub-items meant to be split into separate work); the thread
> shows the design still being debated and no maintainer has settled it; a maintainer
> says the fix touches core internals; it asks for new user-facing product behavior (a
> new tool, option, or capability) that no maintainer has filed, confirmed, or invited
> work on — an untriaged feature wish leaves the decision to add it unmade, however
> polished the write-up; it is a usage or support question ("how do I get this to work?")
> rather than a request for a change. Terseness is NOT a fail: a short body, a bare
> checklist, or a bug without repro steps can still be bounded. A detailed plan is also
> NOT a fail: an issue describing ONE deliverable that enumerates the specific files,
> pages, or sections to touch along the way is a single bounded change (one PR), not an
> umbrella. An umbrella's sub-items are independent pieces of work meant for separate PRs
> or contributors (the same task repeated across a codebase, or a checklist tracking
> other issues). A bug report is NOT an umbrella just because it lists several instances
> of the same defect ("X is missing for A, B, C, etc."): that is one fix. A
> maintainer-filed diagnosis that names the causes to fix, or adds optional follow-up
> suggestions, is a settled spec, not an open debate — grade the core fix asked for.
> Grade the size of the work asked for, not the polish of the write-up.

Reasoning behind its current form: the original wording only distinguished bounded work
by terseness, which let a single well-specified deliverable with a detailed multi-file
plan get misread as an umbrella (`issue-01`), and let a maintainer-filed bug listing
several instances of the same defect, or a maintainer's diagnosis with optional
follow-ups, get misread as unsettled scope (`issue-04`, `issue-19`). I added explicit
carve-outs for each of those shapes so the check grades the size of the actual
deliverable rather than the shape of the write-up.

**Trade-offs**

The same revision that fixed `issue-01`/`issue-04`/`issue-19` also had to hold the line
against accepting things that should stay rejected, so I added a companion fail clause:
new user-facing product behavior that no maintainer has filed, confirmed, or invited work
on is unbounded regardless of how detailed the write-up is. Without that clause the looser
"detailed plan is not a fail" language let `issue-20` (an untriaged bot-filed feature
request with a polished spec) slip through as an incorrect `accept`; with the clause it
correctly fails `bounded-scope` and the verdict returns to `reject`, matching gold. As a
canary against over-loosening, I re-ran `issue-09` (an old maintainer-invited feature
issue that must stay `accept`) with `--only` after each edit — it held `accept` throughout,
so the added carve-outs did not swallow a case they weren't meant to touch. The trade-off
I accept going forward: the check now leans on judgment calls ("is this defect the same
across instances," "did a maintainer settle the design") that a more mechanical rubric
would avoid, so a borderline issue not in this eval set could still be misread in either
direction.

---

## Selection rationale

Graded on whether all three are answered, in your own words. Not on how good the
reasoning is, and not on length — a short honest answer to each earns the full marks.
This is also the basis for the claim comment you write in Unit 2.

**Selection rationale**

1. Fit and time available: I know Python and I'm comfortable with backend/API code, and
   I want practice on real bug diagnosis (error handling, input validation) rather than
   pure documentation work. #68 is a small, well-scoped backend fix, which matches the
   amount of time I have for a first issue.
2. What the verdict identified correctly, and what I weighed that the rubric couldn't:
   the verdict correctly confirmed the repo is alive, the issue is unassigned with no
   open PR, and the fix is a single bounded change with a clear repro. What the rubric
   can't weigh is fit — it ranked #68 above #54 and #73 only because I told it my
   interests; the rubric's required checks alone would have accepted all three equally.
   I also had to personally judge that a classmate's claim comment on #68 doesn't block
   me, per the Path Review house rule.
3. Anticipated difficulty in claiming it: low-to-moderate. The fix itself (returning an
   empty result instead of dividing by zero on an empty index) looks small, but I'll need
   to find and update the matching test(s) and confirm nothing else in the keyword-search
   path assumes a non-empty index, and coordinate lightly with the classmate who already
   commented intent to work on it.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/issue-select/`.
