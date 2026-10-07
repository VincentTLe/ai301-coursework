# Plan: #69, output parser crashes on a top-level JSON array

Issue: https://github.com/codepath/pathreview-ai301-fa26-s3/issues/69
My reproduction: https://github.com/codepath/pathreview-ai301-fa26-s3/issues/69#issuecomment-5904659943
Code: `main` at `2f4e82f`

## Diagnosis

`parse_review_output` hands whatever `json.loads` returns straight to
`_parse_json_output`, which is typed `data: dict` and starts with
`for key, value in data.items():`. A top-level JSON array parses to a
Python `list`, so that loop raises. This is what my reproduction shows:

```
rag\generator\output_parser.py:48: in parse_review_output
    return _parse_json_output(data)

data = ['First feedback item', 'Second feedback item']
...
>       for key, value in data.items():
E       AttributeError: 'list' object has no attribute 'items'

rag\generator\output_parser.py:68: AttributeError
FAILED tests/unit/test_output_parser.py::TestOutputParser::test_json_array_fallback
1 failed, 18 deselected in 1.40s
```

The controls from the same report fit this cause and rule out the
fence regex or the JSON decode step:

- A JSON object parses fine through the same function:
  `[FeedbackSection(section_name='summary', content='Looks good', confidence=0.85, suggestions=[])]`.
- An array inside a ```` ```json ```` fence raises the same
  `AttributeError`. The fence path (line 41) and the raw path (line 48)
  both call `_parse_json_output`, so the fault is in that function, not
  in how the text is found or decoded.

## Scope

In scope: `_parse_json_output` accepts a list as well as a dict. When
it gets a list, it returns one `FeedbackSection` per array item instead
of calling `.items()`. Because both call sites go through this
function, the one change covers raw and fenced arrays. Remove the
`xfail` marker from `test_json_array_fallback`, as the issue asks.

Not in scope:
- Top-level JSON scalars (`"text"`, `42`, `null`). They reach the same
  `.items()` call and would also raise, but the issue is about arrays
  and I have not reproduced the scalar case. I'll mention it in the PR
  as a possible follow-up rather than fix it here.
- The unused `sections = []  # noqa: F841` accumulator in
  `parse_review_output` and the `var-annotated` mypy override for this
  module. CONTRIBUTING says those are separate planted defects to leave
  alone.
- Any change to the dict path, the fence regex, the plaintext
  fallback, or `review_generator.py`.

## Related PRs on this issue

Three open PRs are cross-linked on #69, all from classmates. None of
them has been reviewed by a maintainer yet:

- #79 (pakmultilinks-dot) also makes one section per array item, but
  it expands an object inside the array key by key, the way a
  top-level object is handled.
- #98 (est4ever) sends raw and fenced arrays to the existing plaintext
  fallback instead, so the whole response becomes one
  `general_feedback` section.
- #101 (Builder106) is closest to mine. It uses the same `item_N`
  names, but for an object inside the array it reads `section_name`
  and `suggestions` from the object and uses confidence 0.9.

Mine is the simplest of the per-item versions: strings as-is,
everything else as `json.dumps`, and no fields read from inside items.
If a reviewer prefers one of the other routes, I'll move my regression
test onto that PR instead of pushing a competing one.

## Files

- `rag/generator/output_parser.py`: `_parse_json_output` (signature,
  docstring, and a list branch before the dict loop).
- `tests/unit/test_output_parser.py`: remove the `xfail` marker on
  `test_json_array_fallback`; add one regression test for an array
  inside a ```` ```json ```` fence that checks the section contents.

## Approach

1. In `_parse_json_output`, change the annotation to
   `data: dict | list` and update the Google-style docstring.
2. Before the `data.items()` loop, add: if `data` is a list, build one
   section per item, named `item_1`, `item_2`, ... in order. A string
   item becomes the content as-is; any other item (an object, number,
   nested list) becomes `json.dumps(item)`. Confidence `0.85` and
   `suggestions=[]`, the same values the dict path uses for non-dict
   values. Log `json_output_parsed` with the count, as the dict path
   does, and return.
3. Delete the `@pytest.mark.xfail(...)` decorator on
   `test_json_array_fallback`.
4. Add `test_json_array_in_code_fence`: a fenced `["a", "b"]` returns
   two sections whose contents are `"a"` and `"b"`.
5. Run `make lint`, `make typecheck`, and `make test-unit`.

## Test plan

Re-run the steps from my reproduction on the branch:

```bash
# 1. the H-02 test with its marker removed
.venv/Scripts/pytest tests/unit/test_output_parser.py -k test_json_array_fallback -q
# 2. same test with --runxfail (now identical to 1)
.venv/Scripts/pytest tests/unit/test_output_parser.py -k test_json_array_fallback --runxfail -q
# 3. the controls
.venv/Scripts/python -c "from rag.generator.output_parser import parse_review_output; print(parse_review_output('{\"summary\": \"Looks good\"}'))"
.venv/Scripts/python -c "from rag.generator.output_parser import parse_review_output; fence = chr(96) * 3; print(parse_review_output(fence + 'json\n[\"a\", \"b\"]\n' + fence))"
```

What I expect after the fix:

- Run 1 changes from `1 xfailed` to `1 passed`. Run 2 changes from
  `1 failed` with the `AttributeError` at `output_parser.py:68` to
  `1 passed`.
- The object control prints the same
  `[FeedbackSection(section_name='summary', content='Looks good', confidence=0.85, suggestions=[])]`
  as before, so the dict path is unchanged.
- The fenced-array control prints two sections,
  `item_1` with content `'a'` and `item_2` with content `'b'`, instead
  of a traceback.
- The new `test_json_array_in_code_fence` passes, and
  `make test-unit`, `make lint`, and `make typecheck` stay green with
  no `XPASS(strict)`.

## Risks and unknowns

- Section naming: `item_1`, `item_2` is my choice. Nothing in the repo
  says how array items should be named. `generate_section` in
  `review_generator.py` returns `sections[0]` and does not read the
  name, so the name shouldn't break callers, but a reviewer may prefer
  something else.
- `review_generator.generate_section` keeps only `sections[0]`. With
  one section per item, a multi-item array keeps only its first item
  for that section, while #98's plaintext route keeps the whole text.
  A multi-key top-level object already behaves the same way today, so
  I'm keeping it consistent rather than changing the caller, but it's
  the main trade-off against #98.
- Objects inside an array (`[{"skills": "..."}]`) become one
  JSON-string section each instead of being unpacked like a top-level
  object. That keeps the change small. Unpacking them would be a
  separate decision.
- I have only run this on Windows with Python 3.11.9. I'm assuming CI's
  Python version doesn't matter for this code path, but I haven't
  checked that.

## Deviations

The code change matches the plan: one list branch in
`_parse_json_output`, the H-02 `xfail` marker removed, and one new
test, `test_json_array_in_code_fence`. Commit `60f392f` on
`fix/69-json-array-fallback`. Nothing was added to or dropped from
the scope.

One small change to the test plan itself: the fenced-array control
now builds the fence with `chr(96) * 3` instead of typing backticks.
The command in my posted repro was fine. The problem came from the
script I used to save before/after output, which wrapped each command
in another `bash -c "..."`. Inside those double quotes the backticks
were run as command substitution. The `chr(96)` form avoids that
extra quoting layer. The input and the expected output are the same.

Two things were added to the plan after the build, before posting,
because plan-check flagged them: a "Related PRs on this issue" section
(#79, #98, #101 are cross-linked on #69 and my first draft didn't name
them), and the `generate_section` / `sections[0]` trade-off under
Risks. Neither changes the code.

Results match what the test plan expected. `test_json_array_fallback`
went from `1 xfailed` / `1 failed` to `1 passed` both ways. The object
control printed the same `FeedbackSection(section_name='summary', ...)`.
The fenced `["a", "b"]` returned `item_1` / `item_2` instead of a
traceback. `ruff`, `black --check`, `mypy`, and
`pytest tests/unit -m unit` (377 passed, 52 xfailed) were all green.
