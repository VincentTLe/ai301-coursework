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

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/69

**Verdict output**

Live-mode output of my installed skill, run as:

```
claude -p "issue-select: grade these candidate first issues: https://github.com/codepath/pathreview-ai301-fa26-s3/issues/69 https://github.com/codepath/pathreview-ai301-fa26-s3/issues/61 https://github.com/codepath/pathreview-ai301-fa26-s3/issues/68"
```

All evidence is gathered, so here is the grading. Reference date is today, 2026-09-23.

**All three candidates are accepted.** Every required check passes on each one, and the three tie on preferred checks (two of three each), so the fit profile decides the order.

**Ranked (accepted):**

1. **#69 Output parser crashes on a top-level JSON array fallback.** Best fit: the work is LLM output parsing and data validation in a Python service, plus removing an xfail test marker. Those are the profile's most specifically named interests. Two classmates have commented claiming it. The house rule says that does not block it.
2. **#61 Health check DB probe passes a raw SQL string.** Strong fit: an API endpoint plus a SQLAlchemy query, both named in the profile. It is also the smallest change of the three, essentially wrapping one string in `text()`. No comments, no claims.
3. **#68 Keyword search raises ZeroDivisionError when the index is empty.** Good fit as Python backend and data handling, but it is BM25 retriever logic rather than any of the profile's specifically listed areas. One classmate claim comment, which the house rule ignores.

**Shared repo facts behind the grades:**

| Fact | Value |
|---|---|
| Archived | no |
| Last push | 2026-09-16, 7 days ago |
| Newest default-branch commit | 2026-09-16 by Aburke225 (human, COLLABORATOR) |
| PRs in repo, any state | 0 |
| Contribution policy | docs/CONTRIBUTING.md exists, no statement on AI |
| Maintainer first-response sample | 10 issues, 0 maintainer comments |

**Two notes on the rubric, not on the issues.** First, the claim comments on #69 and #68 would fail `no-active-claim` as the rubric is written, since they are within 90 days with no maintainer reply. They pass only because the scope's Path Review house rule says classmates' claims do not count. Second, `maintainer-responsive` fails on all three, but every sampled issue was opened by the maintainer themselves in a seeded tracker, so "first maintainer response" is structurally absent rather than evidence of an absent maintainer. The check is preferred, so it changes nothing here.

```json
[
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/69",
    "checks": [
      {"name": "repo-alive", "grade": "pass", "evidence": "archived: false; last push 2026-09-16T21:50:20Z, 7 days before reference date 2026-09-23"},
      {"name": "maintainer-active", "grade": "pass", "evidence": "Newest of last 5 main commits is 2026-09-16 by Aburke225 (human COLLABORATOR), 7 days old; all five authored by the same human"},
      {"name": "not-assigned", "grade": "pass", "evidence": "assignees: none"},
      {"name": "no-open-pr", "grade": "pass", "evidence": "No linked PRs in timeline; repo has 0 PRs in any state; no comment links a PR"},
      {"name": "no-active-claim", "grade": "pass", "evidence": "Two student claim comments (Yina-Mu 2026-09-20, tonybuii2003 2026-09-21, both author_association NONE); scope.md house rule: classmates' claim comments do not block a Path Review issue"},
      {"name": "bounded-scope", "grade": "pass", "evidence": "Bug opened by maintainer Aburke225 [COLLABORATOR]: one AttributeError on list.items() in output_parser.py, two files named, xfail marker H-02 to remove; not umbrella, not a question, no closed PRs"},
      {"name": "ai-policy-ok", "grade": "pass", "evidence": "docs/CONTRIBUTING.md and PR template contain no statement on AI; no AI_POLICY.md or AGENTS.md"},
      {"name": "newcomer-label", "grade": "pass", "evidence": "labels: bug, good first issue, rag, tier-1"},
      {"name": "maintainer-responsive", "grade": "fail", "evidence": "Sample of 10 recently updated issues: none received any maintainer comment (all opened by the maintainer, 0 comments from OWNER/MEMBER/COLLABORATOR)"},
      {"name": "starting-point", "grade": "pass", "evidence": "Body names rag/generator/output_parser.py and tests/unit/test_output_parser.py; comment names test_json_array_fallback"}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/61",
    "checks": [
      {"name": "repo-alive", "grade": "pass", "evidence": "archived: false; last push 2026-09-16T21:50:20Z, 7 days before reference date 2026-09-23"},
      {"name": "maintainer-active", "grade": "pass", "evidence": "Newest of last 5 main commits is 2026-09-16 by Aburke225 (human COLLABORATOR), 7 days old; all five authored by the same human"},
      {"name": "not-assigned", "grade": "pass", "evidence": "assignees: none"},
      {"name": "no-open-pr", "grade": "pass", "evidence": "No linked PRs in timeline; repo has 0 PRs in any state; no comments on the issue"},
      {"name": "no-active-claim", "grade": "pass", "evidence": "Comment thread is empty (comments: 0)"},
      {"name": "bounded-scope", "grade": "pass", "evidence": "Bug opened by maintainer Aburke225 [COLLABORATOR]: raw 'SELECT 1' in api/routes/health.py needs sqlalchemy.text(); repro via GET /health given; single file, single change"},
      {"name": "ai-policy-ok", "grade": "pass", "evidence": "docs/CONTRIBUTING.md and PR template contain no statement on AI; no AI_POLICY.md or AGENTS.md"},
      {"name": "newcomer-label", "grade": "pass", "evidence": "labels: bug, good first issue, api, tier-1"},
      {"name": "maintainer-responsive", "grade": "fail", "evidence": "Sample of 10 recently updated issues: none received any maintainer comment (all opened by the maintainer, 0 comments from OWNER/MEMBER/COLLABORATOR)"},
      {"name": "starting-point", "grade": "pass", "evidence": "Body names api/routes/health.py, the GET /health route, and the exact ArgumentError text"}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/68",
    "checks": [
      {"name": "repo-alive", "grade": "pass", "evidence": "archived: false; last push 2026-09-16T21:50:20Z, 7 days before reference date 2026-09-23"},
      {"name": "maintainer-active", "grade": "pass", "evidence": "Newest of last 5 main commits is 2026-09-16 by Aburke225 (human COLLABORATOR), 7 days old; all five authored by the same human"},
      {"name": "not-assigned", "grade": "pass", "evidence": "assignees: none"},
      {"name": "no-open-pr", "grade": "pass", "evidence": "No linked PRs in timeline; repo has 0 PRs in any state; no comment links a PR"},
      {"name": "no-active-claim", "grade": "pass", "evidence": "One student claim comment (acordero4852 2026-09-19, author_association NONE); scope.md house rule: classmates' claim comments do not block a Path Review issue"},
      {"name": "bounded-scope", "grade": "pass", "evidence": "Bug opened by maintainer Aburke225 [COLLABORATOR]: KeywordSearcher.index([]) raises ZeroDivisionError in BM25Okapi, two files named, xfail marker H-01 to remove; not umbrella, not a question, no closed PRs"},
      {"name": "ai-policy-ok", "grade": "pass", "evidence": "docs/CONTRIBUTING.md and PR template contain no statement on AI; no AI_POLICY.md or AGENTS.md"},
      {"name": "newcomer-label", "grade": "pass", "evidence": "labels: bug, good first issue, rag, tier-1"},
      {"name": "maintainer-responsive", "grade": "fail", "evidence": "Sample of 10 recently updated issues: none received any maintainer comment (all opened by the maintainer, 0 comments from OWNER/MEMBER/COLLABORATOR)"},
      {"name": "starting-point", "grade": "pass", "evidence": "Body names rag/retriever/keyword_search.py, tests/unit/test_keyword_search.py, KeywordSearcher.index(), and BM25Okapi"}
    ],
    "verdict": "accept"
  }
]
```

---

## Eval iterations

**Run history**

All runs used the same rubric file, `~/.claude/skills/issue-select/rubric.md`
(the copy uploaded to `tools/issue-select/rubric.md`); I did not edit the rubric
between runs.

1. **Run 0 (aborted, no score).** First full run on Windows crashed before grading
   any bundle: `UnicodeEncodeError: 'charmap' codec can't encode character
   '\U0001f604'` while the harness piped a bundle containing an emoji into
   `claude -p`. Fixed by running with `PYTHONUTF8=1` (no change to the harness).
2. **Run 1 (partial, `--only issue-04,issue-05,issue-09,issue-12,issue-19`):
   agreement 5/5 scored items.** A smoke run on the five bundles I expected to be
   hardest (terse maintainer bug, umbrella, stale claim, AI ban, maintainer-diagnosed
   bug). No bar verdict, since partial runs never print one.
3. **Run 2 (full, `--save-run eval-run.txt`): agreement 19/20 scored items
   (bar: 18/20: PASS).** Categories: claimed 4/4, clear-accept 7/8, dead-repo 3/3,
   policy 1/1, scope 4/4. The one disagreement is issue-19. This is the run in
   `eval-run.txt`.

**Issue analysis**

`issue-19` (zxcalc/zxlive#517, "Selecting large subgraphs in proof mode freezes
the UI").

- My rubric's decision: **reject**. Gold label: **accept**. The gold note calls it a
  "maintainer-diagnosed performance bug with named causes, unclaimed".
- Six of my seven required checks passed on plain repo facts: `archived: no`, last
  push 2026-08-04 (`repo-alive`); newest commit 2026-08-04 by RazinShaikh
  (`maintainer-active`); `assignees: none`, `linked PRs: none`, zero comments
  (`not-assigned`, `no-open-pr`, `no-active-claim`); "no statement on AI"
  (`ai-policy-ok`). The verdict turned on `bounded-scope` alone.
- The grader failed `bounded-scope` with this evidence: "body lists two potential
  causes plus three additional suggestions (multiprocessing, lazy category matching,
  separate rewrite-application thread) — five distinct technical directions across
  matching, threading, and UI, not one scoped change." It read the body's
  "There are two potential causes which should be fixed" and "Additional
  suggestions: 1. … 2. … 3. …" as clause (a) of my check, "explicitly invites many
  separate PRs over an area of the codebase rather than one change".
- Why the rubric read it that way: my clause (a) names the shape of an umbrella
  (a list of separate pieces of work) but not the shape of a diagnosed bug (one
  symptom, a maintainer's list of *causes* and *optional* follow-ups). The
  reminder at the end of the check ("grade the size of the work asked for, not the
  polish of the write-up") covers terse issues, not issues that are generous with
  suggestions. The issue also carries no newcomer label and names no file, so
  nothing in the text pulled the grader back toward "one bounded fix".
- That this sits on the boundary shows in my own runs: the same rubric graded
  issue-19 **accept** in Run 1 and **reject** in Run 2, with the grader quoting the
  same "Additional suggestions" list both times. The fix I would make next is to add
  to `bounded-scope`: "a maintainer-filed bug that lists candidate causes or optional
  follow-up ideas is still one bounded bug; grade the fix to the stated symptom". I
  did not make that edit before the saved run, because 19/20 with every category
  matched is above the bar and I wanted the uploaded rubric and `eval-run.txt` to
  be the same file.

**Check rationale**

Quoted as it is currently written in `tools/issue-select/rubric.md`:

> | `no-active-claim` | The comment thread: comments such as "I'll take this", "working on this", "can I work on this", "I'd like to work on this", "I've started looking at this", with their dates and whether a maintainer replied. | Pass if there is no claim comment dated within 90 days before the reference date. A claim comment older than 90 days is stale and passes. A claim within 90 days still passes only if a maintainer replied to it saying the issue is open to anyone else. A maintainer stating the issue is reserved for a named person ("we are not looking for other contributions") fails regardless of dates. | required |

Why it has this form: the calibration activity showed that "someone commented that
they want it" is not one signal but three. A fresh claim is a real block; a claim from
years ago with no PR behind it is noise (issue-09 and issue-12 both carry one); and a
maintainer's answer can flip either way. So the check names an evidence source
(the comment thread), a numeric window (90 days, measured against the bundle's
capture date, not today), and two explicit overrides: a maintainer saying "go
ahead" reopens a fresh claim, and a maintainer saying "we are not looking for other
contributions" (issue-03, DanielNoord) closes the issue even if the dates would
pass. It is `required` because a claimed issue wastes a first contribution outright.
It says nothing about linked PRs on purpose; those live in `no-open-pr`, so one
disagreement points at one check.

**Trade-offs**

The 90-day window is a guess at how long a silent claimer stays active, and it
cuts both ways. It will miss the case where someone claimed an issue four months
ago, is still quietly finishing a branch, and has not opened a PR yet; my rubric
would tell me the issue is free and I could end up racing them. I accept that
miss because the eval set has no such issue and because in practice a PR usually
appears within weeks of a real claim (`no-open-pr` catches it from then on). The
canaries that confirmed the window are issue-09 (claim from 2022 → stale → accept,
matching gold) and issue-18 (claims on 2026-07-06 and 2026-08-02, inside the window,
plus open PRs → reject, matching gold); both were graded in the full run in
`eval-run.txt` and issue-09 was also in my `--only` smoke run. Nothing else changed
when I settled on 90 days: no accepted issue in the set has a claim comment inside
the window, and every rejected "claimed" issue also fails `no-open-pr` or
`not-assigned`, so the window only decides issue-09.

---

## Selection rationale

**Selection rationale**

1. **Fit to my interests and time.** I am aiming at an AI engineer role and want
   to get stronger at Python backend work. Issue #69 is exactly that: the RAG
   output parser in `rag/generator/output_parser.py` crashes when the LLM returns
   a top-level JSON array instead of an object. Handling messy model output is the
   kind of code I expect to write for real, and it is in Python, the language I have
   used most. The issue names the two relevant files, has a covering test already
   written (marked `xfail`), and estimates 2–4 hours, which fits the time I have
   between units.

2. **What the verdict got right, and what I weighed that the rubric could not.**
   The verdict correctly saw a living repo (commits within days), no assignee, no
   open PR, a bounded bug with a named file and test, and no AI ban in the
   contribution docs. It also correctly ignored the two classmate claim comments
   because of the Path Review house rule. What the rubric cannot see is fit: all
   three candidates it accepted are equally "good first issues" by the checks, so I
   ranked them myself. #61 (SQLAlchemy health check) is the smallest and has no
   competing claims, but it is a one-line `text()` wrap and teaches me less. #68
   (empty BM25 index) is close, but #69 touches LLM output handling, which is
   closest to the work I want to do, so I chose it.

3. **Anticipated difficulty in claiming it.** Two classmates (Yina-Mu on 2026-09-20
   and tonybuii2003 on 2026-09-21) have already commented that they want this issue.
   Under the house rule that is not a block, since credit attaches to my own PR, but
   it means my claim comment in Unit 2 should say what I will do differently or in
   addition (for example, cover both the array fallback and the `.items()` guard,
   and remove the `xfail` marker), and I should expect that a fix may land before
   mine, so I need to reproduce quickly.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/issue-select/`.
