# Plan: issue #68 — keyword search raises `ZeroDivisionError` on an empty index

## Diagnosis

My reproduction (posted upstream in two comments) shows the crash happens inside
`BM25Okapi.__init__`, not in `search()`:

> `ZeroDivisionError` raised inside `rank_bm25`'s `BM25Okapi.__init__`,
> from `KeywordSearcher.index()` at `keyword_search.py:25`, when
> `index()` is called with an empty chunk list — `search()`'s own
> empty-index guard (`if not self.bm25 or not self.chunks`) is correct
> but unreachable here, since `self.bm25` never gets assigned; the
> exception fires while building it.

Reading `rag/retriever/keyword_search.py` against that evidence: `index()`
unconditionally builds `tokenized_corpus = [self._tokenize(chunk["text"])
for chunk in chunks]` and passes it straight to `BM25Okapi(tokenized_corpus)`.
With `chunks == []`, `tokenized_corpus == []`, and `rank_bm25`'s
`BM25._initialize` divides `num_doc / self.corpus_size` with
`corpus_size == 0`. `search()` already has the guard `if not self.bm25 or
not self.chunks: return []` — the completed control run in the second
reproduction comment confirms calling `search()` before `index()` returns
`[]` without crashing. The cause is exactly the asymmetry: `search()`
treats an empty/un-built index as a no-op; `index()` does not treat an
empty input the same way.

## Scope

**In scope:**
- `rag/retriever/keyword_search.py` — add an empty-corpus guard to
  `KeywordSearcher.index()`.
- `tests/unit/test_keyword_search.py` — remove the `@pytest.mark.xfail`
  marker (manifest H-01) from `test_empty_index` per
  `docs/CONTRIBUTING.md`'s "Working on a seeded bug" instructions.

**Not in scope:**
- `search()`, `_tokenize()`, or the `rank_bm25` dependency itself —
  none of them are implicated by the repro evidence.
- `rag/retriever/hybrid.py` — I checked it directly: it imports
  `KeywordSearcher` but only ever calls `.search()` on an
  already-constructed instance, never `.index()`. It is unaffected by
  this change and I'm not touching it.
- The separate whitespace-only-corpus `ZeroDivisionError` I noticed
  while testing (see Risks below) — different trigger, different line
  in `rank_bm25`, not what issue #68 describes.

## Approach

In `KeywordSearcher.index()` (`rag/retriever/keyword_search.py`), add a
guard at the top of the method: if `chunks` is empty, set
`self.chunks = []`, set `self.bm25 = None`, log the same way `search()`
already logs its empty case, and return before tokenizing or
constructing `BM25Okapi`. For a non-empty `chunks`, the existing
tokenize-and-build path is unchanged.

Setting `self.bm25 = None` (rather than leaving a stale value) matters
for the case where `index()` is called a second time with `[]` on a
searcher that previously held a real index — without resetting it, a
subsequent `search()` would use a stale index instead of correctly
reporting empty.

Then, in `tests/unit/test_keyword_search.py`, remove the
`@pytest.mark.xfail(strict=True, reason="issue #68 ...")` marker from
`test_empty_index`, since the fix makes it pass and
`docs/CONTRIBUTING.md` requires dropping the marker as part of the fix.

## Test plan

Re-run my Unit 2 repro steps against the change:

1. **Direct empty-index call** (my repro step 5): run
   `KeywordSearcher().index([])` followed by `.search("python", top_k=10)`.
   Before: raises `ZeroDivisionError`. After: no exception, `search()`
   returns `[]`.
2. **Control — search without indexing** (my repro step 4): confirm
   this still returns `[]` with a warning log, unchanged by the fix.
3. **The repo's own test** (my repro step 6): run
   `pytest tests/unit/test_keyword_search.py -v`. Before: `16 passed,
   1 xfailed`. After (marker removed): all tests including
   `test_empty_index` pass, with no `XPASS(strict)` failure.
4. Full `make lint && make typecheck && make test-unit` locally before
   pushing, per `docs/CONTRIBUTING.md`'s PR checklist.

## Risks and unknowns

- While testing locally I found that a **non-empty** corpus made up
  entirely of whitespace-only text also raises `ZeroDivisionError` in
  `rank_bm25`, but at a different line (`average_idf`, not
  `_initialize`/`avgdl`). That is a different trigger than issue #68
  describes (which is specifically about an *empty* `chunks` list), so
  I'm leaving it out of this fix and flagging it here rather than
  folding an unrelated case into this PR.
- I have not traced every indirect caller of `KeywordSearcher.index()`
  across the codebase (e.g., any ingestion/orchestrator path that
  builds a searcher) — I verified `hybrid.py` directly since it's the
  only other module that imports `KeywordSearcher`, and it never calls
  `.index()`. I have not run the full `make setup` stack (Postgres/
  Docker), since this code path doesn't touch it, matching my Unit 2
  repro's own scope note.

## Deviations

Nothing changed from the posted plan: I added the empty-input guard in
`KeywordSearcher.index()`, reset the stored chunks and BM25 index, logged
the empty-index warning, and removed the strict xfail marker from
`test_empty_index`. The non-empty indexing path remains unchanged.
