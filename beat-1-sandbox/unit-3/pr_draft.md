fix(retriever): guard KeywordSearcher.index() against an empty chunk list

## Summary
`KeywordSearcher.index()` raises a `ZeroDivisionError` from inside
`rank_bm25`'s `BM25Okapi.__init__` when called with an empty chunk list,
because it unconditionally builds a `BM25Okapi` index over an empty
corpus. `search()` already has a guard for an empty/un-built index
(`if not self.bm25 or not self.chunks: return []`), but that guard is
unreachable in this case, since `index()` crashes before `self.bm25` is
ever assigned. This adds the same empty-input guard to `index()` itself,
so an empty chunk list is a no-op (matching how `search()` already
treats the empty case) instead of a crash.

## Issue
Closes #68

## Changes
- `rag/retriever/keyword_search.py`: `KeywordSearcher.index()` now
  checks for an empty `chunks` list up front; if empty, it sets
  `self.chunks = []`, resets `self.bm25 = None`, logs
  `keyword_search_empty_index` (the same warning `search()` already
  logs for its own empty case), and returns before tokenizing or
  constructing `BM25Okapi`. The existing tokenize-and-build path for a
  non-empty `chunks` is unchanged.
- `tests/unit/test_keyword_search.py`: removed the
  `@pytest.mark.xfail(strict=True, reason="issue #68 ...")` marker from
  `test_empty_index`, per `docs/CONTRIBUTING.md`'s "Working on a seeded
  bug" instructions — the fix makes this test pass, so the marker is
  no longer needed and (being `strict=True`) would fail CI as an
  unexpected pass if left in place.

## Testing
- [ ] **CI is green on this PR (all five jobs)** — required for review
- [x] Unit tests pass (`make test-unit`)
- [ ] Integration tests pass (`make test-integration`)
- [x] Linter passes (`make lint`)
- [x] Type checker passes (`make typecheck`)
- [x] New/updated tests cover the changes
- [x] If this fixes a seeded bug: removed its `@pytest.mark.xfail` marker (and any
      matching suppression in `pyproject.toml`)

Full before/after output for every item below is in `test_evidence.md`.

**Direct repro (Unit 2 repro step 5):** before, `KeywordSearcher().index([])`
followed by `.search('python', top_k=10)` raises `ZeroDivisionError`
inside `rank_bm25`. After, it logs `keyword_search_empty_index` and
returns `[]`, no exception.

**Control (Unit 2 repro step 4):** `search()` called before `index()` is
ever called — unchanged before and after, still returns `[]` with the
same warning log.

**The repo's own test:** before, `pytest tests/unit/test_keyword_search.py -v`
→ `16 passed, 1 xfailed`. After (marker removed), `17 passed`, including
`test_empty_index`, with no `XPASS(strict)` failure.

**Full suite and checks**, run on the branch: `pytest tests/unit -v -m
unit` → `376 passed, 52 xfailed` (the 52 are Path Review's other seeded
bugs, unrelated to this fix). `pytest tests/integration -v -m
integration` → `no tests ran` (Path Review has no integration tests
yet; pasted as-is per the assignment's own note — the checkbox above is
left unticked because there is nothing to run). `ruff check .` → `All
checks passed!`. `mypy api/ core/ ingestion/ rag/ agent/ safety/` →
`Success: no issues found in 76 source files`.

The CI checkbox above is left unticked because CI only runs after this
PR opens.

## Screenshots / Demo
N/A — this is a backend library fix with no UI-visible behavior; the
before/after terminal output above is the complete demonstration of the
change.

## Notes for Reviewers
**AI use:** I used Claude Code to help draft the empty-input guard in
`keyword_search.py` and this PR description from my already-posted
plan (`plan.md`, accepted in Unit 3). I wrote and verified the test
plan myself, traced the root cause by hand before posting my Unit 2
reproduction, and personally re-ran every repro step and repo check
listed above on this branch before opening this PR.

**Deviations from the plan:** none. The implementation matches
`plan.md`'s Approach section exactly: the empty-input guard resets
both `self.chunks` and `self.bm25` (not just one), which matters if
`index()` is ever called a second time with `[]` after previously
holding a real index — without resetting `self.bm25`, a later
`search()` would use a stale index instead of correctly reporting
empty. See `plan.md`'s Risks and unknowns section for two things
intentionally left out of scope: a separate whitespace-only-corpus
`ZeroDivisionError` I found while testing (different trigger, different
line in `rank_bm25`, not what this issue describes), and indirect
callers of `index()` beyond `rag/retriever/hybrid.py`, which I checked
directly and confirmed never calls `.index()`.
