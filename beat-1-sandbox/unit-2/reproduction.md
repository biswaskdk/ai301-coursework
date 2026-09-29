# Unit 2 — Claim and Reproduce

Path: `beat-1-sandbox/unit-2/reproduction.md`

Record of your claim and reproduction on the issue you chose in Unit 1, and of the
evaluation runs that produced `eval-run.txt`. This file is graded at the path above; a copy
kept anywhere else in the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Your identity upstream

**GitHub username**

biswaskdk

---

## Posted upstream

**Claim comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/68#issuecomment-5896971638

Hi — I'd like to take this as a first contribution. I've traced the `ZeroDivisionError`: it's raised inside `rank_bm25`'s `BM25Okapi.__init__` (dividing `num_doc` by `corpus_size`) when `KeywordSearcher.index()` is called with an empty chunk list — not in `search()`, which already has an empty-index guard that never gets reached because `index()` crashes first. Next I'll post a full reproduction report here (environment, steps, and the failing/xfail test output), then go from there.

**Reproduction comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/68#issuecomment-5897023934

Environment: Windows 11 (Git Bash), Python 3.12.10, rank-bm25 0.2.2 (repo pins `rank-bm25>=0.2.2`, so this is the minimum pinned version — no mismatch with what the repo targets), structlog 26.1.0, pytest 9.1.1. I installed only these three packages in a fresh venv rather than running the full `make setup` stack (no Postgres/Docker involved): `rag/retriever/keyword_search.py` only imports `rank_bm25` and `structlog`, so isolating it is sufficient to exercise this code path.

Steps:

1. Clone the fork, stay on `main` (no changes made to source).
2. `python -m venv .venv-repro && source .venv-repro/Scripts/activate`
3. `pip install rank-bm25 structlog pytest`
4. Control run — `search()` before `index()` is ever called:
   ```
   $ python -c "from rag.retriever.keyword_search import KeywordSearcher; s = KeywordSearcher(); print(s.search('python', top_k=10))"
   2026-09-29 ... [warning] keyword_search_empty_index
   []
   ```
   No crash: `search()`'s own `if not self.bm25 or not self.chunks` guard catches this case.
5. Reproduction — call `index([])` first, then `search()`:
   ```
   $ python -c "from rag.retriever.keyword_search import KeywordSearcher; s = KeywordSearcher(); s.index([]); print(s.search('python', top_k=10))"
   Traceback (most recent call last):
     ...
     File ".../rag/retriever/keyword_search.py", line 25, in index
       self.bm25 = BM25Okapi(tokenized_corpus)
     File ".../rank_bm25.py", line 27, in __init__
       nd = self._initialize(corpus)
     File ".../rank_bm25.py", line 52, in _initialize
       self.avgdl = num_doc / self.corpus_size
   ZeroDivisionError: division by zero
   ```
6. Confirmed against the repo's own test: `python -m pytest tests/unit/test_keyword_search.py -v` → 16 passed, `test_empty_index` `XFAIL` (its marker names this exact issue, manifest H-01).

Actual: `ZeroDivisionError` raised inside `rank_bm25`'s `BM25Okapi.__init__`, from `KeywordSearcher.index()` at `keyword_search.py:25`, when `index()` is called with an empty chunk list. The crash happens in `index()`, before `search()` is ever reached — `search()`'s empty-index guard (`if not self.bm25 or not self.chunks`) is correct but unreachable here, since `self.bm25` never gets assigned; the exception fires while building it.

Expected, per the issue: `index([])` followed by `search()` should return `[]`, the same as the already-correct "search without calling index" case.

Conclusion: reproduced. This matches the issue's description exactly — `ZeroDivisionError` on an empty index, library named as `rank_bm25`, no unrelated symptom.

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

1. Full run (first pass): `agreement: 17/20 scored items  (bar: 18/20: below the bar)`
2. `--only pkg-01,pkg-09,pkg-10,pkg-20,pkg-02` (after revising two checks): `agreement: 5/5 scored items`
3. Full run with `--save-run eval-run.txt` (final, committed): `agreement: 19/20 scored items  (bar: 18/20: PASS)`

**Package analysis**

`pkg-19`. My rubric's decision: `accept`. Gold label: `reject`. Reasoning: this issue
(vuejs/core#15205) has a repro report that's genuinely solid — environment, steps, and a
reproduced CSS-scoping bug that matches the issue exactly. Gold rejects it purely on the
claim comment, which is interchangeable "assign-me" boilerplate promising a guaranteed
2-day fix ("Kindly assign it to me, I will fix it within 2 days guaranteed"). My rubric has
no check that grades the claim comment's own honesty or specificity in isolation — my five
checks cover the environment record, the steps, whether the artifact matches the issue, the
report's stated confidence against its evidence, and repo conventions/disclosure. None of
those directly fail a claim comment for over-promising a fix and a timeline it can't back.
In my first full run this package happened to land on `reject` anyway, because my
"Repo conventions" check's wording at the time was strict enough that the model read the
bug-report template's "reproduction steps, expected, actual" ask as something the *claim*
comment also had to satisfy, and failed it there for an unrelated reason. Loosening that
check's wording (see Trade-offs) removed that accidental catch, and the package flipped to
`accept` with nothing left in my rubric to fail it on. This names a real gap: a rubric with
no check for claim-comment specificity/over-promising can't buy back a boilerplate,
guaranteed-timeline claim, no matter how good the report attached to it is.

**Check rationale**

`Behavior matches issue`, quoted as currently written in `rubric.md`:

> Pass if the artifact shows the same behavior the issue describes (same error, same
> symptom, same failure point); ALSO pass if the report's stated conclusion is an honest
> cannot-reproduce (see Outcome check) and the artifact shows the attempted steps ran to
> completion without producing the issue's behavior, with any environment difference from
> the issue called out — showing no matching behavior is the correct outcome there, not a
> gap. Fail if the report claims to reproduce the issue but the artifact shows a different
> or merely adjacent behavior. Unclear if an artifact is present but too partial to compare
> against what the issue describes, under either conclusion

Reasoning behind its current form: the original wording only had a fail branch for "no
behavior at all," which correctly catches a report that asserts reproduction with nothing
to show, but also wrongly caught an honest, evidenced cannot-reproduce report (`pkg-09`,
`pkg-10`) — both showed their attempted steps running to completion and explicitly named
what differed from the issue's environment, and gold scores both `accept`. I added the
cannot-reproduce carve-out so the check grades whether the artifact matches what the
*stated conclusion* requires, rather than always demanding the issue's behavior appear.

**Trade-offs**

The same revision that fixed `pkg-09`/`pkg-10` also touched a second check
("Repo conventions and disclosure"), which I had to correct at the same time: its original
wording let a bug-report template meant for people *filing* a new issue get applied to a
contributor's claim/repro comments on an issue someone else already filed, wrongly failing
`pkg-01` for not restating version/search-confirmation language that template asks of
reporters, not claimants. I re-ran both fixes together against canaries
(`pkg-20` for the disclosure-wall category, `pkg-02` for wrong-target) with `--only` before
the confirming full run, and both canaries held their correct `reject`, so the fix did not
loosen the checks that are supposed to catch those shapes. The trade-off I accept going
forward: as the package analysis above shows, that same loosening also removed an
accidental correct rejection on `pkg-19`, and my rubric still has no check dedicated to a
claim comment's own honesty/specificity independent of the repro report — a well-reproduced
package attached to boilerplate, over-promising claim language can still pass as written.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/repro-check/`.
