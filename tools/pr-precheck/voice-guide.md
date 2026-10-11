# Voice guide: how I talk upstream

## Who I am in threads

I'm making my first contribution to an open-source project. I don't
know this codebase's history or its maintainers' unwritten norms yet,
so I read before I write and I don't assume my read is the only one.
Readers should expect specifics tied to this issue, not general
enthusiasm, and should expect me to say plainly when I'm not sure of
something rather than talk around it.

## Rules I write by

### Rule: name the specific thing, not the category

A comment should point at the exact line, version, command, or output
that's relevant to this issue, not describe the general kind of thing
I looked at.

- Wrong: "I looked into the ingestion pipeline and found some issues."
- Right: "The web parser at `ingestion/web.py:42` strips the `<h2>`
  text when it's indented, which is why portfolio pages with nested
  headings lose their section titles."

### Rule: promise only investigation, never a fix or a date

A claim comment says what I'm about to do next, not what I'll deliver
or when. I don't know yet whether my fix will work, so I don't say it
will.

- Wrong: "I'll have a PR up by tomorrow fixing this."
- Right: "Next I want to check whether the parser change also breaks
  the existing README-heading path, and I'll report back what I find."

### Rule: a plan comment commits to the approach, but not the outcome

Unlike a claim comment, a plan comment states a decided cause and a
decided change — that's the point of posting it — so I don't hedge the
diagnosis or scope with "I think maybe." But I still don't promise the
fix will work on the first try, or name a delivery date; those are
outcomes, not the approach itself.

- Wrong (under-committing, fine for a claim comment but not a plan):
  "I think it might be something in the refresh logic, not sure yet."
- Wrong (over-promising an outcome): "This will fully fix the issue,
  PR up by Friday."
- Right: "The post-push refresh scope misses the branch-commits
  context; plan is a one-change fix in the sync controller's push
  callback, verified against a manual check across both commit views
  and a force push."

### Rule: state a cannot-reproduce as plainly as a reproduce

If my steps don't trigger the bug, I say that directly, with what I
tried and what I saw instead — not softened into "might be fixed
already" when I don't actually know that.

- Wrong: "This seems to be working fine now, might already be fixed?"
- Right: "I followed the steps in the issue on [environment] and did
  not see the described behavior; output attached. I have not checked
  earlier versions."

### Rule: no diagnosis I haven't actually traced

I don't name a root cause unless I've read the code path that produces
it. A guess dressed as a finding wastes a maintainer's time checking
something I never checked myself.

- Wrong: "This is probably a race condition in the queue."
- Right: "I haven't traced the cause yet — next step is stepping
  through `queue.py`'s consumer loop to see where the ordering
  breaks."

### Rule: match the repo's own template before adding my own voice

If the repo's issue template or CONTRIBUTING.md asks for something
specific (a version confirmation, a disclosure line, a particular
format), I give that first, in its own words, before anything else I
want to say.

- Wrong: skipping the "confirm you searched for duplicates" line
  because I already know I'm not duplicating anything.
- Right: "I searched existing issues and didn't find a duplicate of
  this. [rest of comment]"

### Rule: a PR description claims exactly what the diff contains, nothing more

A description is read by a maintainer deciding whether to spend review
time on my work, and it is checked against a diff they can see — so it
never claims a scope, a test, or a behavior the diff doesn't actually
show. A deliberately deferred piece is named as deferred, not folded
into "implements the plan" language that implies completeness.

- Wrong: "Implements the fix and updates the docs." when the diff has
  no doc change.
- Right: "Implements the fix in `keyword_search.py`. Docs update
  deferred — see Notes for Reviewers."

### Rule: a disclosed shortfall is stated as a fact, not apologized for

When my plan or the PR leaves something out on purpose, I say what and
why once, plainly, in the section meant for it — I don't hedge it with
apologetic language or bury it in a paragraph where a reviewer could
miss it.

- Wrong: "Sorry, I didn't have time to fully test the edge case, hope
  that's ok."
- Right: "Not covered: the whitespace-only-corpus crash I found while
  testing — different trigger than this issue, out of scope for this
  fix; noted in plan.md under Risks."

### Rule: AI use is disclosed in my own words, specific to this change

Where a PR asks for an AI-use disclosure, I say concretely how I used
it on this change (e.g., which parts I drafted vs. reviewed myself),
not a boilerplate line that would read the same on any PR.

- Wrong: "AI tools were used to assist with this PR."
- Right: "Used Claude Code to draft the empty-corpus guard and this
  description from my posted plan; I wrote and verified the test plan
  and reran all reproduction steps myself."

## Things I never post

- A fix estimate or timeline I haven't earned ("should be done by
  Friday").
- "Same as above, can confirm" on a shared issue — my reproduction
  goes up in my own words even if a classmate already posted theirs.
- A root-cause claim I haven't traced myself, even if it sounds
  plausible from the stack trace alone.
- Any comment I haven't first run through this skill against my own
  draft.
- A PR description that claims fidelity to the plan without checking
  the diff against it first.
- A boilerplate AI-disclosure line that doesn't name what I actually
  did on this specific change.
