# Unit 3 — Plan and Build

Path: `beat-1-sandbox/unit-3/plan-and-implement.md`

---

## Posted upstream

**GitHub username**

VincentTLe

**Plan comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/69#issuecomment-6032315115

Pasted text, exactly as posted:

Plan for #69, built from [my reproduction above](https://github.com/codepath/pathreview-ai301-fa26-s3/issues/69#issuecomment-5904659943).

**Diagnosis.** `_parse_json_output` is typed `data: dict` and starts with `for key, value in data.items():`. A top-level array parses to a `list`, so that line raises. My run showed `data = ['First feedback item', 'Second feedback item']` failing with `AttributeError: 'list' object has no attribute 'items'` at `output_parser.py:68`. A JSON object parsed fine through the same function, and a fenced array failed at the same line. So the fault is in `_parse_json_output` itself, not the fence regex or `json.loads`.

**Change.** When `_parse_json_output` gets a list, it returns one `FeedbackSection` per item (`item_1`, `item_2`, ...). A string item is kept as the content, and any other item is stored as `json.dumps(item)`. Both the raw and the fenced call sites go through this function, so one change covers both. The `xfail` marker comes off `test_json_array_fallback`, and one new test checks the section contents for a fenced array.

**Not changing:** the dict path, the fence regex, the plaintext fallback, and the `# noqa: F841` accumulator in `parse_review_output` (CONTRIBUTING says to leave those planted lines alone). Top-level scalars like `"text"` or `42` hit the same `.items()` call, but I haven't reproduced that, so I'll note it in the PR as a possible follow-up instead of fixing it here.

**Related PRs.** Three open PRs are linked here, and this plan is close to them:
- #79 also makes one section per item, but it expands an object inside the array key by key.
- #98 sends arrays to the plaintext fallback, so the whole response becomes one `general_feedback` section.
- #101 is the closest. It uses the same `item_N` names, but it reads `section_name`/`suggestions` from objects inside the array and uses confidence 0.9.

Mine is the plainest per-item version and reads no fields from inside items. If a reviewer prefers one of the others, I'm happy to put my regression test on that PR instead of opening a competing one.

**Test plan and status.** I've built this on `fix/69-json-array-fallback` in my fork and re-ran my repro commands against it:
- `test_json_array_fallback` went from `1 xfailed` / `1 failed` (with `--runxfail`) to `1 passed`.
- The object control prints the same `FeedbackSection(section_name='summary', ...)` as before.
- The fenced `["a", "b"]` returns `item_1`/`item_2` instead of a traceback.
- `ruff`, `black --check`, `mypy` and the unit suite are green.

**Open questions.** `review_generator.generate_section` keeps only `sections[0]`, so a multi-item array keeps only its first item there. #98's route keeps the whole text instead. A multi-key object already behaves the same way, so I kept them consistent. The `item_N` names and leaving objects inside arrays as JSON strings are also my choices. I'm happy to change any of these.

I used Claude Code to help read the code, draft this plan, and run the test commands in my terminal. I reviewed the diff and the output before posting.

---

## Your branch

**Branch**

fix/69-json-array-fallback

**Evidence**

Unit 2 repro steps run in my fork's clone. The control commands are the
same as in my posted repro. The fenced-array one-liner builds the fence
with `chr(96) * 3` so Bash doesn't treat the backticks as command
substitution (see Deviations in `plan.md`).

Before, on `main`:

```
$ git log --oneline -1
2f4e82f chore: track five more manifest entries against the tracker

$ .venv/Scripts/pytest tests/unit/test_output_parser.py -k test_json_array_fallback -q -p no:cacheprovider
x                                                                        [100%]
18 deselected, 1 xfailed in 1.21s

$ .venv/Scripts/pytest tests/unit/test_output_parser.py -k test_json_array_fallback --runxfail -q -p no:cacheprovider 2>&1 | tail -6
E       AttributeError: 'list' object has no attribute 'items'
rag\generator\output_parser.py:68: AttributeError
=========================== short test summary info ===========================
FAILED tests/unit/test_output_parser.py::TestOutputParser::test_json_array_fallback
1 failed, 18 deselected in 1.23s

$ .venv/Scripts/python -c "from rag.generator.output_parser import parse_review_output; print(parse_review_output('{\"summary\": \"Looks good\"}'))"
2026-10-07 01:06:18 [info     ] json_output_parsed             section_count=1
[FeedbackSection(section_name='summary', content='Looks good', confidence=0.85, suggestions=[])]

$ .venv/Scripts/python -c "from rag.generator.output_parser import parse_review_output; fence = chr(96) * 3; print(parse_review_output(fence + 'json\n[\"a\", \"b\"]\n' + fence))" 2>&1 | tail -4
  File "D:\AI301\pathreview\rag\generator\output_parser.py", line 68, in _parse_json_output
    for key, value in data.items():
                      ^^^^^^^^^^
AttributeError: 'list' object has no attribute 'items'
```

After, on `fix/69-json-array-fallback`:

```
$ git log --oneline -1
60f392f fix(rag): handle a top-level JSON array in the output parser

$ .venv/Scripts/pytest tests/unit/test_output_parser.py -k test_json_array_fallback -q -p no:cacheprovider
.                                                                        [100%]
1 passed, 19 deselected in 1.24s

$ .venv/Scripts/pytest tests/unit/test_output_parser.py -k test_json_array_fallback --runxfail -q -p no:cacheprovider 2>&1 | tail -6
.                                                                        [100%]
1 passed, 19 deselected in 1.17s

$ .venv/Scripts/python -c "from rag.generator.output_parser import parse_review_output; print(parse_review_output('{\"summary\": \"Looks good\"}'))"
2026-10-07 01:14:40 [info     ] json_output_parsed             section_count=1
[FeedbackSection(section_name='summary', content='Looks good', confidence=0.85, suggestions=[])]

$ .venv/Scripts/python -c "from rag.generator.output_parser import parse_review_output; fence = chr(96) * 3; print(parse_review_output(fence + 'json\n[\"a\", \"b\"]\n' + fence))" 2>&1 | tail -4
2026-10-07 01:14:41 [info     ] json_output_parsed             section_count=2
[FeedbackSection(section_name='item_1', content='a', confidence=0.85, suggestions=[]), FeedbackSection(section_name='item_2', content='b', confidence=0.85, suggestions=[])]
```

Repo checks on the branch:

```
$ .venv/Scripts/ruff check .
All checks passed!
$ .venv/Scripts/black --check rag/generator/output_parser.py tests/unit/test_output_parser.py
2 files would be left unchanged.
$ .venv/Scripts/mypy api/ core/ ingestion/ rag/ agent/ safety/
Success: no issues found in 76 source files
$ .venv/Scripts/pytest tests/unit -q -m unit
377 passed, 52 xfailed, 3 warnings in 34.44s
```

## Eval iterations

**Run history**

1. Full run 1: 19/19 scored items agreed. pkg-02 came back `ERROR`
   ("no stdin data received"), so the harness treated the run as partial
   and didn't write `eval-run.txt`.
2. Full run 2, with no file changes: the same 19/19 and the same pkg-02
   `ERROR`. pkg-02 is the only package containing an emoji (`📦`). On
   Windows, Python's text-mode stdin uses cp1252, which can't encode it,
   so the prompt never reached `claude`.
3. `--only pkg-02` with `PYTHONUTF8=1`: 1/1 (accept, matching gold).
4. Full run 3 with `PYTHONUTF8=1` and no rubric changes:
   **agreement 20/20 scored items (bar: 18/20: PASS)**, categories
   clear-accept 7/7, scope-creep 4/4, thread-convention 2/2,
   unbuildable 3/3, wrong-cause 4/4. This is the run in `eval-run.txt`.
   (I started a duplicate full run while run 3 was still going, because
   I thought run 3 had died. I stopped it partway, before it wrote
   anything, so run 3 is the last full run and the file it saved is the
   one committed.)

**Package analysis**

pkg-16 (pandas-dev/pandas#57666, wrong-cause). My rubric decided
`reject` and the gold label is `reject`. The plan is long and confident:
"The cast is where the data is damaged, so the cast is what must
change". It proposes re-padding strings in `_finalize_pandas_output`.
My procedure has the grader read the repro evidence before the plan
and note what each control rules out. Step 4 of the repro says the
column "arrives as `int64` with value `1`; the zeros are already gone
in the parsed table, before any cast to string could run." So
`diagnosis-grounded` failed with "Plan blames the post-read astype,
but repro step 4 shows the column is already int64 1 in pyarrow's
parsed table before any cast runs". `fix-at-cause` failed because the
re-padding happens "downstream of where pyarrow's typed parse lost the
zeros". `thread-engaged` also failed: the comment never mentions the
MEMBER's explanation or the thread's `column_types` direction. The
plan is bounded and its test plan is decisive, and those checks passed,
which is right. This package is why the rubric says "the damage is
already present before the blamed step runs" instead of only "the
cause contradicts the evidence": a grader can apply the first by
pointing at one step.

**Check rationale**

| `fix-at-cause` | The plan's change (Change, Approach, or Proposed changes), read against where the repro evidence shows the fault first appears. | Pass if the change acts at the point where the stated, grounded cause sits, so the reproduced behavior can no longer happen. Fail if the change only hides or works around the behavior while the evidence locates the fault elsewhere: a documentation-only workaround for a code defect, catching or suppressing the error, adding retries, or repairing already-damaged output downstream of where the data was lost. Clamping, validating, or handling the bad value at the site where it is produced or consumed counts as fixing at the cause. | required |

I split this out of `diagnosis-grounded` because the lecture's
"targets the symptom while the evidence points at the cause" family
can fail even when the stated cause is right. pkg-04 (fzf) is the
example: the diagnosis that fzf's console input loop keeps reading
keys is consistent with the repro, but the plan only documents the
`> /dev/tty` workaround. The list of workaround shapes (docs only,
suppressing the error, retries, downstream repair) comes from the
symptom fixes in the eval set: pkg-04's docs, pkg-18's `recover()`,
pkg-15's retry wrapper, and pkg-16's re-padding. Each is a concrete
thing a grader can point at. The last sentence was added before the
first run, on purpose, after reading pkg-02. Its fix is a
`saturating_sub` clamp, and without that sentence a grader could call
any clamp "hiding the underflow" and fail a gold accept.

**Trade-offs**

The clamp exemption gives up some strictness. A plan that clamps or
validates a bad value where it is consumed passes `fix-at-cause` even
when the real bug is that the value should never be produced. If a
plan clamped a negative width in the printer while the evidence showed
the width calculation upstream was wrong, this check would pass it, and
only `diagnosis-grounded` could catch it. I accepted that miss so
pkg-02 (bat) stays an accept: its saturating clamps sit right where the
underflow happens, and that is the fix the issue points at. How I know
nothing else changed: every rubric file was frozen from run 1 through
run 3 (the same `rubric.md` hash `15c875e150fb2afb` is in
`eval-run.txt`). pkg-02 graded accept in the `--only` re-run and in run
3, and the four packages whose plans are workarounds (pkg-04, pkg-15,
pkg-16, pkg-18) still reject. In run 3, pkg-04, pkg-16, and pkg-18 fail
`fix-at-cause` directly. pkg-15 passes it, because its core
timeout-to-500 ms fix really is at the cause. Its retry wrapper is
extra work, not a replacement, so it is caught by `scope-bounded`
instead. This check reads only the main change and leaves bundled
extras to the scope check.

---

Related paths: `plan.md` and `eval-run.txt` in this directory; your skill's files in
`tools/plan-check/`.
