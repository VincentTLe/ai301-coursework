# Rubric: is this pull request ready to submit?

## How to read the package

"The plan" is the accepted plan the PR claims to implement: its
change, Scope, Not-in-scope, Files, and Test plan lines, plus every
note the plan itself records (a known limit, a deferral, or, live, the
`## Deviations` section of `plan.md`). A plan's deliverables are the
distinct things it promises to put in the diff: each fix site, each
named test, and each named doc, changelog, or i18n item.

"A hunk" is one `@@` block of the diff. Read each hunk by what its
removed and added lines actually do, not by what the description says
they do.

"The repo's stated asks" are the repo-facts `pull requests:` line (the
PR template's sections, checklist items, required links and entries)
and the `contribution policy` line (including any AI policy it
names). Live: `.github/PULL_REQUEST_TEMPLATE.md`,
`docs/CONTRIBUTING.md`, and the house rules in `scope.md`.

"The primary repro" is the case the issue reports and the plan's test
plan re-runs first: the exact command, input, or UI steps that showed
the bug. A "control" is a case that already worked before the fix.

Treat every package as AI-assisted work. A classmate's PR on the same
issue (live mode) never fails a check.

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| `diff-within-plan` | Every hunk in the diff and every commit in the commit list, read against the plan's change, Scope, Not-in-scope, and Files lines and its recorded notes. | Pass if every hunk is part of the planned change: the fix itself, the tests the plan names or regression tests for the issue's case, the same fix at a sibling site the plan names, an entry the repo's stated asks require (changelog, whatsnew, release note, i18n string), or work the plan's notes record as a deviation. Fail if any hunk or commit does work the plan does not name: refactoring or rewriting nearby code, a rename pass, a new option, setting, flag, or config field, a dependency bump, rewording text the fix does not touch, editing a file the plan never names, or anything the plan lists as not in scope. This holds even when the description says the PR is "exactly the plan". Fail also if the diff never makes the plan's change at all. | required |
| `plan-delivered` | Each deliverable the plan names, read against the diff, then against the plan's recorded notes and the description. | Pass if every deliverable appears in the diff, or the plan itself defers it (a not-in-scope line, a stated known limit, or a deviation note) and the description says so too. Fail if a deliverable the plan promises (a fix site, a named test, a docs page, a changelog entry) is missing from the diff and neither the plan's notes nor the description disclose the gap. A shortfall that is disclosed in the plan and restated in the description is honest and passes. | required |
| `description-matches-diff` | The claims in the PR title and description about what the PR contains (fidelity claims such as "exactly as planned" or "no other changes", files and areas touched, entries, docs, and tests added, the cases the tests cover, the behavior that changed), read against the diff and the commit list. | Pass if every such claim is true of the diff. Fail if the diff plainly contradicts a claim a reviewer would rely on: a fidelity claim while the diff does more or less than the plan, or a claimed file, entry, doc, or test that the diff does not contain. Loose wording about size or emphasis is not a contradiction (for example "one function touched" when the fix adds a helper for that function). Grade only what the description claims. Extra hunks it stays silent about are `diff-within-plan`'s job. | required |
| `repro-rerun` | The test-evidence section (and any before/after in the description), read against each case the plan's test plan names and the repro evidence's trigger. | Pass if the evidence re-runs the primary repro on the branch, with its trigger written out (the command, input, or UI steps) and the observed after-result shown or described specifically (output, exit code, value, visible behavior), matching the plan's expected-after, with the before shown or quoted. Every other case the test plan names (a second failure mode, a second repro, a control) must at least be reported with its observed result, and a sentence is enough for these ("control unchanged"). Fail if the evidence is only a summary with no trigger and no before ("tested locally, works now", "verified on my machine for a day", "all tests pass"); if it runs a different case than the primary repro (a control, a single-file case, a different request, a path the fix does not change); or if a case the test plan names is never mentioned. | required |
| `repo-checks-reported` | The test-evidence section and description, read against the checks the plan's test plan names and the checks the repo's stated asks require (test suite, lint, format, type check, pre-commit, code generation). | Pass if each such check's outcome is reported by name (the command or suite, such as "cargo test -p ignore passes" or "make lint: All checks passed"), or a check that failed or could not run is reported honestly with its reason. Also pass when neither the plan nor the repo names any check. Fail if a check that the test plan or the repo requires is never reported, or if the only report is an unnamed "all tests pass". | required |
| `diff-clean` | The added and removed lines of every hunk, and the commit list. | Pass if nothing a reviewer must skip rides along with the change. Fail if the diff adds any of these: commented-out code (a commented-out debug print, or a "first attempt" kept "for reference"); a debug print or trace left in (`eprintln!("DEBUG ...")`, a stray `print(...)`); a dead function or variable (unused, named `_unused_...`, or kept alive with `allow(dead_code)`); a TODO or FIXME on a line the fix does not need; or a block whose removed and added lines do the same thing and differ only in formatting or order (regrouped imports, a re-wrapped struct literal, re-indented or identical lines printed again). Comments that explain the fix are not debris. A block that the diff re-lists next to a real change in it (the same call re-printed with one argument changed) is not churn. Live: `plan.md`, `comment.md`, `pr_draft.md`, or `test_evidence.md` in the diff is debris. | required |
| `template-honored` | The repo's stated asks (PR template sections, checklist items, required issue link, required changelog or whatsnew entry, contribution-doc PR rules), read against the title, description, commits, and diff. Live: the PR template, `docs/CONTRIBUTING.md`, and `scope.md`'s house rules, including the branch name. | Pass if every ask is met with real content: each named section is filled (or says "N/A" with a reason), a requested issue link (`closes #N`, `fixes #N`) names this issue, a requested checklist is answered, and a requested entry is present in the diff. An ask met in different words passes ("Type of changes: Bug fix" answers a type-of-changes checklist). Also pass when there is no template and the contribution docs' asks are met or there are none. Fail if any ask is visibly ignored: a required checklist is absent, a required issue link is missing, a required entry is missing from the diff, or a section is empty or left as template boilerplate. Live: also fail a branch name that is not `<type>/<issue-number>-<slug>` with this issue's number, or a ticked box the evidence does not back. An unticked CI box with "CI runs after the PR opens" is honest, not missing. | required |
| `ai-disclosure` | The repo-facts `contribution policy` line (and any AI policy it names), read against the description. Live: the course's standing ask to disclose AI use in the description counts as a stated policy, together with any policy in `docs/CONTRIBUTING.md`. | If the policy requires disclosure of AI use in pull requests or of "all AI usage in any form": pass only if the description names the AI tool and the extent of its help, and fail if it does not, however good the PR is. If the policy requires the text to be the author's own words: pass if the description reads as first-person writing by the author or says it is in their own words. Pass when there is no stated AI policy, or the policy only asks the contributor to understand, review, or test the code. | required |
| `commits-describe-change` | The commit list. | Pass if each commit message says what that commit changes, in the repo's stated commit convention when it has one. Fail on placeholder messages ("wip", "fix", "misc cleanups while debugging", "fmt"). | preferred |
| `regression-test-targets-case` | The tests the diff adds or changes, read against the issue's reported case. | Pass if at least one added or changed test exercises the issue's own case and would fail on the old code. Fail if the added tests only cover a control or an unrelated behavior, or the PR adds no test for a behavior change. | preferred |

## Verdict rule

- `accept` if and only if every `required` check grades `pass`.
- `reject` if any `required` check grades `fail` or `unclear`.
  `unclear` counts as `fail`: a PR the grader cannot verify from the
  package is not ready to submit. Grade `unclear` only when the
  evidence a check needs is absent from the package (for example, no
  test-evidence section at all), never as a softer fail.
- `preferred` checks are reported but never change the verdict.
- Length, confidence, and polish earn nothing. A terse PR that meets
  every pass condition is ready, and a disclosed shortfall is not a
  failure of any check.
