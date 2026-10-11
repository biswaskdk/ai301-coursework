# Unit 3 — Plan and Build

Path: `beat-1-sandbox/unit-3/plan-and-implement.md`

Record of your plan, your branch, and the evaluation run that produced
`eval-run.txt`.

## Posted upstream

**GitHub username**

biswaskdk

**Plan comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/68#issuecomment-6030173646

Plan, building on the reproduction above: index() builds a BM25Okapi unconditionally, so with chunks == [] it reaches rank_bm25's avgdl = num_doc / self.corpus_size with corpus_size == 0. search() already guards this case (if not self.bm25 or not self.chunks: return []); index() doesn't. This lands on the same empty-corpus-guard cause and fix several others on this thread already reproduced and proposed (@acordero4852, @SingLZ, among others) — noting that here since the thread's independently converged on it, not claiming it as new.

Change: in KeywordSearcher.index() (rag/retriever/keyword_search.py), when chunks is empty, set self.chunks = [] and self.bm25 = None and return before building BM25Okapi, instead of passing it an empty tokenized corpus. Non-empty input goes through the existing path unchanged. I'll also drop the strict=True xfail marker on test_empty_index (manifest H-01), per CONTRIBUTING's instructions for a seeded bug.

Scope is this one guard plus the marker removal. I checked hybrid.py, the only other module importing KeywordSearcher: it only calls .search(), never .index(), so it's untouched by this.

Test: re-run my repro steps above — index([]) then search() should return [] with no exception, the search-without-indexing control should stay unchanged, and test_keyword_search.py should go from 16 passed, 1 xfailed to all passing.

One open item I'm flagging rather than folding in: a non-empty, whitespace-only corpus also raises ZeroDivisionError in rank_bm25, but at a different line (average_idf) — that's a different trigger than this issue, so it's out of scope here.

## Your branch

**Branch**

fix/68-empty-keyword-index

**Evidence**

Before (repro run on unmodified `main`, Python 3.12.10, rank-bm25 0.2.2):

Command:

```powershell
.\.venv-repro\Scripts\python.exe -c "from rag.retriever.keyword_search import KeywordSearcher; searcher = KeywordSearcher(); print('control:', searcher.search('python', top_k=10)); searcher.index([]); print('after index([]):', searcher.search('python', top_k=10))"
```

Output:

```text
2026-10-06 23:03:52 [warning  ] keyword_search_empty_index
Traceback (most recent call last):
  File "<string>", line 1, in <module>
  File "C:\Users\biswa\Desktop\pathreview-ai301-fa26-s3\rag\retriever\keyword_search.py", line 25, in index
    self.bm25 = BM25Okapi(tokenized_corpus)
                ^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "C:\Users\biswa\Desktop\pathreview-ai301-fa26-s3\.venv-repro\Lib\site-packages\rank_bm25.py", line 83, in __init__
    super().__init__(corpus, tokenizer)
  File "C:\Users\biswa\Desktop\pathreview-ai301-fa26-s3\.venv-repro\Lib\site-packages\rank_bm25.py", line 27, in __init__
    nd = self._initialize(corpus)
         ^^^^^^^^^^^^^^^^^^^^^^^^
  File "C:\Users\biswa\Desktop\pathreview-ai301-fa26-s3\.venv-repro\Lib\site-packages\rank_bm25.py", line 52, in _initialize
    self.avgdl = num_doc / self.corpus_size
                 ~~~~~~~~^~~~~~~~~~~~~~~~~~
ZeroDivisionError: division by zero
control: []
```

Before, focused test result recorded in my reproduction comment: `python -m pytest tests/unit/test_keyword_search.py -v` → 16 passed and `test_empty_index` XFAIL.

After, the same repro command:

```text
2026-10-06 23:04:17 [warning  ] keyword_search_empty_index
control: []
2026-10-06 23:04:17 [warning  ] keyword_search_empty_index
2026-10-06 23:04:17 [warning  ] keyword_search_empty_index
after index([]): []
```

After, `python -m pytest tests/unit/test_keyword_search.py -v`:

```text
tests/unit/test_keyword_search.py::TestKeywordSearcher::test_empty_index PASSED
============================= 17 passed in 0.21s ==============================
```

The focused test file passed in full. After writing this record, I reran the full unit suite, lint, and typecheck
from the Path Review repository. `pytest tests/unit -v -m unit` reported
376 passed and 52 xfailed; `ruff check .` passed; and
`mypy api/ core/ ingestion/ rag/ agent/ safety/` reported no issues in
76 source files. The integration command collected no tests and exited
with pytest code 5, which is expected because this repository has no
integration tests. The outputs are recorded in `test_evidence.md`.

## Eval iterations

**Run history**

1. Full run saved at 2026-10-07T02:41:33Z: `agreement: 20/20 scored items  (bar: 18/20: PASS)`. Category matches: clear-accept 7/7, scope-creep 4/4, thread-convention 2/2, unbuildable 3/3, wrong-cause 4/4.

**Package analysis**

`pkg-16`. My rubric decided `reject`, matching the gold label `reject`. The candidate plan diagnoses the pandas post-read cast as the point where leading zeros are lost. The reproduction's step 3 shows pyarrow's native parser preserves `"01"` when told to parse the column as a string, while step 4 shows inference has already converted it to `int64` and removed the zero. The `Diagnosis grounded in evidence` check requires the stated cause to account for every result, including controls, so the inference result contradicts the proposed post-read-cast diagnosis.

**Check rationale**

From `tools/plan-check/rubric.md`, the `Diagnosis grounded in evidence` pass condition:

> Pass if the stated cause accounts for every result in the repro evidence, including any result that rules out a competing explanation (e.g., the behavior still occurring with a suspected component disabled or removed from the loop).

This check makes the decision depend on the full reproduction, especially control results, rather than accepting a plausible headline diagnosis alone. In `pkg-16`, the pyarrow controls identify parsing-time inference as evidence against the candidate plan's post-read-cast diagnosis.

**Trade-offs**

The diagnosis check is deliberately strict about contradictions in repro evidence. This may hold a plan whose proposed patch could still improve the symptom if the author has not established the cause, but it avoids approving a plan that targets a mechanism contradicted by a control. `pkg-16` illustrates that trade-off: the plan offers a concrete change, but its diagnosis does not fit the evidence about when the zeros disappear.
