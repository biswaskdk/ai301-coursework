# Test evidence: issue #68 — keyword search `ZeroDivisionError` on an empty index

## 1. Direct empty-index call (Unit 2 repro step 5)

Before, on `main`:

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

After, on `fix/68-empty-keyword-index`:

```
$ python -c "from rag.retriever.keyword_search import KeywordSearcher; s = KeywordSearcher(); s.index([]); print(s.search('python', top_k=10))"
2026-10-11 00:02:15 [warning  ] keyword_search_empty_index
2026-10-11 00:02:15 [warning  ] keyword_search_empty_index
[]
```

No exception; `search()` returns `[]` as expected.

## 2. Control — search without indexing (Unit 2 repro step 4)

Before, on `main`:

```
$ python -c "from rag.retriever.keyword_search import KeywordSearcher; s = KeywordSearcher(); print(s.search('python', top_k=10))"
2026-10-11 00:02:10 [warning  ] keyword_search_empty_index
[]
```

After, on `fix/68-empty-keyword-index`:

```
$ python -c "from rag.retriever.keyword_search import KeywordSearcher; s = KeywordSearcher(); print(s.search('python', top_k=10))"
2026-10-11 00:02:15 [warning  ] keyword_search_empty_index
[]
```

Unchanged, as expected: this path was already correct and the fix does
not touch it.

## 3. The repo's own test (Unit 2 repro step 6)

Before, on `main`:

```
$ python -m pytest tests/unit/test_keyword_search.py -v
...
tests/unit/test_keyword_search.py::TestKeywordSearcher::test_empty_index XFAIL [ 52%]
...
======================== 16 passed, 1 xfailed in 0.54s ========================
```

After, on `fix/68-empty-keyword-index` (marker removed):

```
$ python -m pytest tests/unit/test_keyword_search.py -v
...
tests/unit/test_keyword_search.py::TestKeywordSearcher::test_empty_index PASSED [ 52%]
...
============================= 17 passed in 0.38s ==============================
```

All 17 tests pass, including `test_empty_index`; no `XPASS(strict)`
failure, confirming the marker removal was correct.

## 4. Full suite and repo checks (per `docs/CONTRIBUTING.md`'s PR checklist)

Run on `fix/68-empty-keyword-index`:

```
$ python -m pytest tests/unit -v -m unit
...
================ 376 passed, 52 xfailed, 3 warnings in 10.89s =================
```

(The 52 remaining `xfailed` are Path Review's other seeded bugs, unrelated
to issue #68; none of them are mine to fix this round.)

```
$ python -m pytest tests/integration -v -m integration
collecting ... collected 0 items
============================ no tests ran in 0.20s ============================
```

Path Review has no integration tests yet, so this "no tests ran" result
is expected, per the assignment's own note, and is pasted here as-is.

```
$ ruff check .
All checks passed!
```

```
$ mypy api/ core/ ingestion/ rag/ agent/ safety/
Success: no issues found in 76 source files
```

The unit tests, lint, and typecheck pass. The integration command
reports `no tests ran` (pytest exit code 5), as expected for this repo;
its checkbox remains unticked in `pr_draft.md`.
