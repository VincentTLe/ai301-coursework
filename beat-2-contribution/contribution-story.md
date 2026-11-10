# Contribution: WLS support for `ritest()` (pyfixest#463)

Path: `beat-2-contribution/contribution-story.md`

This file is Assignment 5, and it is graded at the path above; a copy kept anywhere else in
the repository is not read. **Keep every section header below under its exact name** — the
format criteria grade exactly that. Your course repo link is the whole submission: no tool
files and no evidence uploads are part of Assignment 5.

**Contribution Number:** 1
**Student:** Vincent Le
**GitHub Username:** VincentTLe
**Issue:** https://github.com/py-econometrics/pyfixest/issues/463
**Pull Request:** https://github.com/py-econometrics/pyfixest/pull/2024

One repository, built across units 5 to 8, telling the whole story of your real
contribution. Fill it as you work, not at the end — the sections below ask for evidence that
is far easier to write down as it happens than to reconstruct in the last weekend.

Contribution Number stays `[1]` unless you make more than one contribution this term; it
just numbers them.

---

## Toolkit

Every story section below, from Why I Chose This Issue through Pull
Request, ends with a Toolkit block: which tool did that phase's work
and how you used it, what it produced, what difficulties or
shortcomings it presented, and what you changed or would change
because of them. Together the blocks are the thread the rubric's
Toolkit points read, and they are the raw material for your unit-9
portfolio, so keep them current as you work rather than
reconstructing them later.

A Toolkit block looks like this (copy it into each story section and
fill it there):

```text
**Toolkit**

- Tool: [which tool did this phase's work, and how you used it]
- What it produced: [described in a sentence or a short excerpt]
- Difficulties: [what fought you or fell short, or "none met"]
- What changed: [the patch, revision, or workaround, what you would
  update, or "nothing"]
```

This section also keeps one short dated entry per week of tool work,
starting below: what you built or changed that week, what broke, and
what you changed because of it. The blocks tell the story where it
happened; these entries are the toolkit's own timeline.

All four entries are graded. Keep every heading below, and fill each
one in the week it belongs to.

**Unit 5: built issue-scout; its first wild scan accepted 0 of 5, so the strategy and the rubric were patched**

Built with Claude Code: issue-scout installed over my unit-1 skill, plus a search strategy of three `gh search issues`
queries scoped to 18 Python LLM-tooling organizations, since a query across all of GitHub returned 100 results from
4.5 hours of activity, with 29 of their 45 repos under 50 stars. The first scan graded 5
issues and accepted none: 4 already had open linked PRs, and `maintainer-active` rejected docling-pipelines#343
because five dependabot bumps filled its five-commit window, although humans had merged five commits in the previous
24 hours. The fixes were `-linked:pr` in every query (it removes all four taken issues before grading), a live-mode
look-back of up to 20 commits in `maintainer-active`, and whole-word matching in a hardware filter that had dropped
issues for "npu" inside "input". Re-graded, #343 is accepted, and the four unit-1 eval bundles that test that check
still match the gold labels (4/4).

**Unit 6: installed the voice guard, patched it to also read PowerShell commands, and wrote the repo memory for pyfixest**

The course guard only read commands from Claude Code's Bash tool, but on Windows Claude Code often runs `gh` through
its PowerShell tool, so a comment posted that way would skip my voice check. I changed `check_voice.py` to read both
tools and to split PowerShell commands with `posix=False`, since PowerShell keeps backslashes in paths literal; after
that the hook checked and passed my #463 claim (guard log, 2026-10-26 21:44). The repo memory, `CLAUDE.local.md`, was
written late, on 2026-11-04: it drops the template's `@AGENTS.md` line because pyfixest's own CLAUDE.md already
imports AGENTS.md, and it keeps the draft-PR rule, the branch and commit format, the pixi commands, and the finding
that `within_data` is not WLS-transformed, so a new session starts from those instead of re-reading four files.

**Unit 7: installed the ship-gate, wrote the pyfixest manifest, and patched the gate for PowerShell and five command forms it misread**

The gate only listened to Claude Code's Bash tool, so a `gh pr create` run through the PowerShell tool would have
opened a PR with no checks. I widened its matcher to `Bash|PowerShell` (my first try, `Powershell`, matched nothing),
and Claude patched `check_gate.py` to split PowerShell commands and to stop five forms it misread in hand tests (a
`cd` in front, `$(...)`, two `gh pr create` lines, a missing file name, `~`). My manifest runs the targeted `ritest`
tests, `ty`, the pre-commit hooks on changed files and `test-py`. The first branch run stopped on `mixed-line-ending`
because this repo forces LF and my Windows git used `core.autocrlf=true`; with `core.autocrlf=input` for the clone,
the second run passed all four checks and my pr-precheck (10 of 10). One gap I can't hide: GitHub's GraphQL API
failed twice on `gh pr create` after both hooks had passed it, so #2024 was opened through the REST API with the same
title and body, and that last call never went through the hook.

**Unit 8: installed the PR monitor, wrote my playbook from pyfixest's real response times, and merged `master` instead of the rebase it drafted**

On 2026-11-09 the monitor, installed and run by Claude Code, read #2024 as BRANCH-STALE (no comment or review in 5
days, and a conflict with `master` since 0.70.0 shipped the day before), so under my playbook (no nudge while the
branch conflicts, 7 quiet days before a first nudge) it drafted a rebase plan and no comment.
The draft fell short in three places: it guessed which files conflicted (only `docs/changelog.qmd` did, because my
entry sat under the now-released 0.70.0), it worried about #2014, which is still a draft, and it planned a rebase plus
`--force-with-lease`, while pyfixest's `git-and-pr-style.md` lets an agent rewrite history only with explicit approval.
So I merged `master` instead (the repo squash-merges, so nothing was rewritten), moved the entry to a new 0.80.0 (In
Development) section, re-ran my manifest's four checks (all passed, `test-py` 6764) and pushed `efbf3dc0`.
Writing the playbook changed my number too: the five merged outside PRs got a first answer within 18 minutes to 2.6 days, but they were small fixes,
and open outside feature PRs like #1885 and #2014 have waited weeks, so I set 7 days for my one nudge.

---

## Why I Chose This Issue

On 2026-10-26 I gave my scout three issues directly: hackclub/nephthys#104, scikit-hep/pyhf#783 and
py-econometrics/pyfixest#463. It accepted all three and ranked #463 last. It called #463 "ambitious", because it needs
econometrics knowledge and an R setup for the comparison tests, which is the furthest from my fit profile. It also
gave two leads that turned out to matter: the functions the issue links had moved to
`pyfixest/estimation/post_estimation/ritest.py`, and the repo's AGENTS.md says never to return numbers for an
unsupported path, so I would need evidence that weighted results are right.

I picked #463 anyway, against my scout's order. My fit profile asks for Python work with the tests around it, and
#463 comes with the clearest test target of the three: the maintainer's to-do list names the functions to change and
asks for tests against R's `ritest`, so I would know when the fix is right. nephthys#104 fits my backend interest
better on paper, but the repo has 21 stars and no test suite, so I couldn't show a fix works there. pyhf#783 is a
small output change on an issue that has been quiet since 2020. In pyfixest (361 stars), six `ritest` fixes had been
merged on 2026-10-24, so the maintainers are active on exactly this code. What I hope to learn: how to prove a
numerical change is correct (two algorithms that must agree, and a reference implementation in R), and how to work in
a repo with written rules for AI-assisted PRs.

My claim comment: https://github.com/py-econometrics/pyfixest/issues/463#issuecomment-6432447860 (2026-10-27).
The scout held up on the facts it checked: the guard location, the moved files and the fast-path `weights` argument
were all where it said. Where it fell short: no check asks whether the code an issue links still exists (two of the
three links had moved), and `maintainer-active` was ambiguous when the newest commit is a dependabot bump (pyhf).

**Toolkit**

- Tool: issue-scout (my Unit 5 build: search strategy plus rubric), run on three issue URLs I gave it on 2026-10-26.
- What it produced: accept for all three; #463 ranked third and "ambitious", with the moved-file and
  AGENTS.md notes above.
- Difficulties: no rubric check for "the linked code still exists"; `maintainer-active` read "the newest of the
  five commits" strictly would have rejected pyhf#783 because of one dependabot bump.
- What changed: nothing patched yet. I would add a "linked code still exists" check and change
  `maintainer-active` to "the newest human-authored commit, or bot merge of a human's PR, among the five".

---

## Understanding the Issue

**Problem Description**

`ritest()` (randomization inference) can't be used on a weighted regression. If you fit `pf.feols(...,
weights="weights")` and call `fit.ritest("X1")`, it stops with an error. The fast algorithm behind it also never
applies the weights, so just deleting the error would give wrong p-values.

**Expected Behavior**

For a weighted `feols`, `ritest()` returns a p-value where every permuted draw is estimated by weighted least
squares (WLS) with the same weights, like R's `ritest` on a weighted `fixest` model. The fast and slow algorithms
give the same draws.

**Current Behavior**

On `master` (2aba80fd) it raises `NotImplementedError: Regression Weights are not supported with Randomization
Inference.` The same model without weights runs.

**Affected Components**

- `pyfixest/estimation/models/feols_.py`: `Feols.ritest()`, where the guard is.
- `pyfixest/estimation/post_estimation/ritest.py`: `_get_ritest_stats_slow`, `_get_ritest_stats_fast` and
  `_run_ri`, the two algorithms.
- `pyfixest/estimation/refit.py` (`refit()`) and `pyfixest/estimation/internals/fit_.py` (`fit_ols()`), which show
  how weights are applied.
- Tests: `tests/test_ritest.py`, `tests/r_test_comparisons.R` and `tests/data/ritest_results.csv`.

**Toolkit**

- Tool: Claude Code reading the five files above from my clone, with the repo's AGENTS.md loaded through its
  CLAUDE.md, and the findings saved in my `CLAUDE.local.md`.
- What it produced: the cause in two parts. The guard blocks weights, and behind it the fast path solves OLS
  because `within_data` is demeaned but not `sqrt(w)`-transformed (`fit_ols` says "Demeaned but not
  WLS-transformed").
- Difficulties: the issue's links point to an old path (`pyfixest/estimation/ritest.py`), and it calls the fast
  path "slightly more complex" without saying why. The name `within_data` doesn't show that the weights are
  missing.
- What changed: I wrote the subsystem section of `CLAUDE.local.md` so later sessions start from the cause.

---

## Reproduction Process

**Environment Setup**

- Windows 11 Pro (10.0.26100), Git Bash and PowerShell, `gh` logged in as VincentTLe.
- Fork `VincentTLe/pyfixest`, cloned to `D:\AI301\pyfixest`, with `upstream` pointing at
  py-econometrics/pyfixest. Reproduced on `master` at 2aba80fd.
- pixi 0.81.0 (the repo runs every Python, test and R command through pixi), installed with
  `winget install prefix-dev.pixi`. Then `pixi install -e py312`: Python 3.12.12, numpy 2.4.2, numba 0.64.0,
  pyfixest 0.60.0 as an editable install.
- Challenges: pixi wasn't installed, so I installed it with winget. `pixi install` also rewrote `pixi.lock`, because
  my pixi is newer than the v0.76.2 that CI uses, and AGENTS.md says not to change `pixi.lock` outside dependency
  work. I restored it with `git checkout pixi.lock` and ran every later command with `--frozen`.
  For the R reference values (the `py312-r` environment, R 4.4.3), installing R's `ritest` failed because its
  dependency `mvtnorm` had to compile from source and the Fortran build stopped with
  ``No rule to make target `$(FCLIBS_XTRA)'``. I installed the prebuilt conda-forge packages `r-mvtnorm`,
  `r-multcomp`, `r-parallelly` and `r-pbapply` into that environment with `conda install --no-deps`, and then
  `ritest` 0.1.0 installed from r-universe.

**Steps to Reproduce**

1. In the clone on `master`: `pixi install -e py312`, then `git checkout pixi.lock`.
2. Run `pixi run --frozen -e py312 python -c "<script>"`, where the script fits
   `pf.feols("Y ~ X1 + f3 | f1", data=pf.get_data(N=1000, seed=2999), weights=w)` for `w` in `None` and
   `"weights"`, and calls `fit.ritest("X1", reps=200, rng=np.random.default_rng(1234))` on each.
3. Observed result:

```
weights=None: Pr(>|t|)=0.0000
weights=weights: NotImplementedError: Regression Weights are not supported with Randomization Inference.
```

**Reproduction Evidence**

- **Commit showing reproduction:** https://github.com/VincentTLe/pyfixest/commit/2aba80fda223e23a53e56b25abcf88d43b9c6b7a
  (`master` of my fork, where the output above comes from).
- **Screenshots/logs:** the output above, and the guard-deleted run below.
- **My findings:** The unweighted control runs and the weighted fit stops at the guard, so the issue still
  happens. Then the guard was deleted locally, with nothing else changed, and the same 200 draws were run with both
  algorithms. The fast path gave different permuted coefficients from the slow path (max difference 2.10e-01 without
  fixed effects, 2.82e-01 with `| f1`), and with `| f1` the p-value was wrong (fast 0.0000, slow 0.0050). So the fix
  needs more than deleting the guard: the slow path refits with the weights through `refit()`, but the fast path
  (`_run_ri`) solves on data that are not WLS-transformed.

**Toolkit**

- Tool: repro-check (my Unit 2 skill, with the real-repo scope from Unit 6), run in a fresh `claude -p` session from
  the clone on the claim plus my draft report `repro-comment.md`.
- What it produced: accept, all 7 required checks plus `control-run` and `template-asks-covered`. It also flagged
  that "would return OLS draws" read as fact and is only exact without fixed effects, that `python -c "<script>"`
  breaks if pasted because the script has double quotes, and that nobody could rerun the second finding from the
  comment.
- Difficulties: the environment took most of the time (pixi missing, the lock rewrite, the R compile failure). The
  claim promised a report "within a week", and it came a day late.
- What changed: before posting I changed the run step to "save as `repro_463.py`", added the second script in a
  `<details>` block, added a line that the p-values can match while the draws differ, backed the cause with the
  run after the `sqrt(w)` change (3.44e-15 and 1.22e-15), and said plainly that the report was a day late. Posted
  on 2026-11-04, and the voice guard passed it through the hook:
  https://github.com/py-econometrics/pyfixest/issues/463#issuecomment-6625919567

---

## Solution Approach

**Analysis**

`Feols.ritest()` in `pyfixest/estimation/models/feols_.py` raises
`NotImplementedError: Regression Weights are not supported with Randomization Inference`
as soon as `self.options.has_weights` is true, so a weighted `feols` can't run randomization
inference at all. Deleting that guard is not enough, because the two algorithms behind it
handle weights differently:

- **Slow path** (`_get_ritest_stats_slow` in `pyfixest/estimation/post_estimation/ritest.py`)
  refits the model for every permutation through `refit()` (`pyfixest/estimation/refit.py`).
  `refit()` replays `fit.options`, weights included, so every permuted fit is already a WLS fit.
  This path is correct once the guard is gone.
- **Fast path** (`_get_ritest_stats_fast` -> `_run_ri`, numba) reuses the model's
  `within_data.response` and `within_data.design`. Those matrices are demeaned but not
  WLS-transformed: `fit_ols()` in `pyfixest/estimation/internals/fit_.py` multiplies by
  `sqrt(w)` only at solve time ("Demeaned but not WLS-transformed"). `_run_ri` demeans each
  permuted treatment with the weights, then calls `lstsq_numba` on the unscaled matrices, so it
  does an unweighted solve instead of WLS. Without fixed effects that is plain OLS and the weights
  are ignored completely; with fixed effects it is an unweighted solve on weight-demeaned data.
  The repro shows the result: with only the guard deleted, fast and slow permuted coefficients
  differ by up to 0.28. `ritest()` picks the fast path by default when numba is installed, so removing the
  guard alone would return wrong numbers.

**Proposed Solution**

1. In `_run_ri`, multiply the response and the other regressors by `sqrt(weights)` once before
   the loop, and each permuted (demeaned) treatment inside the loop, before `lstsq_numba`. That is
   the same square-root transform `fit_ols()` uses. With unit weights it multiplies by 1.0, so
   unweighted results don't change.
2. In `Feols.ritest()`, allow regression weights for the linear OLS/WLS estimator with
   `weights_type="aweights"` (the default), and keep raising `NotImplementedError`, with a message
   that names what is supported, for the cases this change does not cover.

What I'm deliberately NOT changing:

- `weights_type="fweights"` stays blocked. Permuting a row with frequency weight k moves k
  identical observations together, which is a different assignment design from permuting the
  expanded data, and R's `ritest` has no frequency-weight notion to compare against.
- Weighted `fepois`/`feglm`/`feiv` stay blocked. They only take the slow path and there's no
  reference to check weighted results against; the issue asks for WLS.
- No change to the p-value, its standard error and CI, cluster handling, or `randomization-t`
  (it always uses the slow path, which already refits with weights).

**Implementation Plan**

1. Branch `feat/ritest-wls` from `upstream/master`.
2. `pyfixest/estimation/post_estimation/ritest.py`: apply the `sqrt(weights)` transform in
   `_run_ri` and update its docstring.
3. `pyfixest/estimation/models/feols_.py`: narrow the weights guard in `ritest()` to fweights and
   non-OLS estimators; mention weights in the docstring.
4. `tests/test_ritest.py`: add a weighted fast-vs-slow test (no FE, one FE, two FE; with and without
   cluster). The permuted coefficients must match to 1e-8, which fails before step 2 because the
   fast path solves OLS. Add tests that fweights and a weighted `fepois` still raise.
5. R reference values: add weighted `feols` cases to `tests/r_test_comparisons.R`, add their rows (and a
   `weights` column) to `tests/data/ritest_results.csv`, and extend `test_vs_r` to compare them. The
   issue's "with stratification" belongs to #459 (draft #2014); `ritest()` has no `strata` on `master`,
   so these cases are weights only. If the rows can't be generated on my machine, I'll say so in the PR
   and leave them for a maintainer to generate.
   **Changed 2026-11-04 (dropped):** the rows were generated (R 4.4.3, fixest 0.14.0, ritest 0.1.0), but
   5 of 9 failed against this branch, with pyfixest p-values systematically higher (0.0190 vs 0.0084).
   R's `ritest` source shows why: every draw runs `fixest::demean(Xmat, fmat)` and `.lm.fit()` without
   the weights, while the observed coefficient comes from the weighted fit. Emulating that in Python
   (weighted observed coefficient against unweighted draws) reproduces R's numbers (0.0095 vs 0.0084,
   0.0047 vs 0.0047, 0.4908 vs 0.4910, 0.5060 vs 0.4981). So R's `ritest` is not a reference for weighted
   fits, and committing those rows would lock in its behavior. The R script and CSV are unchanged; the
   weighted check is the fast-vs-slow test, whose slow path refits the weighted `feols`. The PR says so.
6. `docs/changelog.qmd`: one entry under 0.70.0, as `docs/developer/architecture.md` asks for features.
   Draft #2014 touches the same functions and test files; if it lands first, I rebase onto it.
7. Re-run the reproduction, `tests/test_ritest.py`, the Python baseline (`test-py`) and `ty`, and
   record the output in `test_evidence.md`.
   **Added 2026-11-04:** `test-py` failed one test, `tests/test_errors.py::test_ritest_error`, which
   asserted the old behavior (a weighted `feols` raises). It now checks that `fweights` still raise.

Deviations: `plan.md` was written before any code, but the fix and its tests (steps 2-4) were built while
plan-check was still grading; its accept didn't change them. Its review added the stratification note in step 5
and step 6 (the changelog and the #2014 rebase), and those were built after the verdict. Step 5 was then dropped
and step 7 gained the `test_errors.py` fix, both dated above and in the Implementation Notes; the PR says so.

**Toolkit**

- Tool: plan-check (my Unit 3 skill, with the real-repo scope from Unit 6), run in a fresh `claude -p` session from
  the clone on `plan.md` and `plan-comment.md` (the plan comment stays unposted, as the course asks).
- What it produced: accept, all 7 required checks plus both preferred checks. It also found draft PR #2014
  (stratified RI), which changes `_run_ri`, `r_test_comparisons.R` and `test_ritest.py` right next to my lines,
  and pointed out that the issue's "with stratification" wording was copied from #459's to-do.
- Difficulties: it called the fast path's output "OLS" too loosely (true only without fixed effects); my rubric
  still refers to the Path Review house rules though `scope.md` now has real-repo rules; and the ship-gate crashed
  while it ran (see Unit 7 entry).
- What changed: I rewrote the cause as "an unweighted solve", put "my reading of why" in front of the mechanism in
  the comment, named #2014 with a rebase promise, explained the stratification wording, added the changelog step,
  and copied those changes into the plan above.

---

## Testing Strategy

Four observable checks, each run on `master` (2aba80fd) or on the branch (c3e2ad77):

1. **The reproduction, before and after.** On `master` the weighted fit raises `NotImplementedError` and the
   unweighted control returns `Pr(>|t|)=0.0000`. On the branch the weighted fit returns `Pr(>|t|)=0.0050`.
2. **Fast vs slow on the same draws.** The slow algorithm refits the weighted `feols` for every draw through
   `refit()`, which is pyfixest's normal WLS path, already compared with R `fixest` in the repo's own tests. So the
   fast algorithm is right when it matches it. With only the guard deleted they differed by up to 0.28; on the branch
   by at most 3.44e-15. The new `test_wls_algos_internally` asserts that to 1e-8 for no FE, `|f1` and `|f1+f2`, with
   and without `cluster`. It failed 6 of 6 with the `_run_ri` change stashed and passed with it.
3. **What still raises.** `test_ritest_rejects_unsupported_weights` (fweights `feols`, weighted `fepois`) and the
   updated `tests/test_errors.py::test_ritest_error`.
4. **The repo's checks**, from my ship manifest: `tests/test_ritest.py` with the extended and R-comparison cases
   (97 passed), `test-py` (6644 passed, 3801 skipped, 7 xfailed, 17 min), `ty` (all checks passed) and the
   pre-commit hooks on the changed files. The release contract skipped all 1344 cases (no pyfixest 0.60.0 baseline
   for win-64, and it has no `ritest` cases). The R suites weren't run locally; CI runs them.

What I tried and dropped: weighted R reference rows. R's `ritest` ignores the weights in its draws (see
Implementation Notes), so it can't check this change, and the PR explains that to the maintainers.

**Toolkit**

- Tool: the repo's own checks through pixi, written into `ship-manifest.md` so the ship-gate runs them in a fresh
  shell, plus R 4.4.3 with `ritest` 0.1.0 for the attempted reference values.
- What it produced: the before/after numbers above, a test that fails 6/6 without the fix, `test-py` 6644 passed
  after one real failure, and the finding that R's `ritest` is not a weighted reference.
- Difficulties: `test-py` took 28 minutes while the R script ran in parallel, over the gate's 25-minute limit per
  check (17 minutes alone). The R run took 38 minutes and its regenerated unweighted rows moved by up to 0.0061. The
  release contract has no Windows baseline.
- What changed: I timed `test-py` alone before keeping it in the manifest, put the changed-file lint before it (it
  needs a clean tree and `test-py` rewrites `tests/texfiles/test.tex`), kept the committed R values, and dropped the
  weighted R rows.

---

## Implementation Notes

**2026-11-04 (setup and reproduction)**

Forked and cloned pyfixest, installed pixi, built the `py312` environment, and restored the `pixi.lock` that
`pixi install` had rewritten (from then on every command used `--frozen`). Reproduced the `NotImplementedError` on
`master` 2aba80fd, with the unweighted fit as the control. Before writing any fix I wanted to know whether deleting the
guard was enough, so the guard was deleted locally and the fast and slow algorithms were compared on the same draws.
They disagreed by up to 0.28, which moved the real work from the guard to the fast path.

**2026-11-04 (the fix and the scope decision)**

Branch `feat/ritest-wls` from `upstream/master` (the repo names branches by outcome, not issue number). `_run_ri` now
multiplies the response, the other regressors and each permuted treatment by `sqrt(weights)` before the solve, like
`fit_ols` does. Decision: allow only analytic weights on `feols`. `fweights` stay blocked, because permuting one row
moves all k copies together, which is a different design from permuting the expanded data. Weighted `fepois`/`feglm`
stay blocked because nothing checks them. The new weighted fast-vs-slow test failed 6 of 6 with the `_run_ri` change
stashed, and passed with it. Committed as `aff179a1` (`feat(ritest): support regression weights for feols`).

**2026-11-04 (wrong turns: line endings and a lint stash)**

The repo's `mixed-line-ending` hook failed on my first commit try: this clone has `core.autocrlf=true`, and the edited
lines had different line endings from the rest of the file. The hook fixed them, and the staged diff had no CR left. Then
`prek run --from-ref upstream/master --to-ref HEAD` stashed my uncommitted test edits to run, and couldn't put them
back because Rscript had `tests/r_test_comparisons.R` open. `tests/test_ritest.py` came back without my R-comparison
edits, and I restored it from a copy I had made before staging. Lesson: run the changed-file lint only on a clean tree,
which is how the ship-gate runs it.

**2026-11-04 (first ship-gate run: STOP on line endings)**

The first manual ship-gate run passed `tests/test_ritest.py` (306 s) and `ty`, then stopped on my changed-file lint:
`mixed-line-ending` failed on four files. The commits were clean (0 CR in every blob), but the files on disk weren't.
Making them all CRLF didn't help, and `.pre-commit-config.yaml` showed why: the hook runs with `--fix=lf`, so this
repo wants LF on disk. The cause was my Windows git default `core.autocrlf=true`. I set `core.autocrlf=input` for
this clone only; the lint then passed, `git diff` was empty and no commit changed.

**2026-11-04 (R reference values)**

Added nine weighted cases (`Y~X1+f3` with `X1` and `X1=-0.75`, `Y~D_cluster+f3` clustered by `group_id`, each
without fixed effects, `|f1` and `|f1+f2`) to `tests/r_test_comparisons.R`, with a new `weights` column in
`tests/data/ritest_results.csv`. The R run took 38 minutes. Two surprises: regenerating the 24 existing unweighted
rows moved their p-values by up to 0.0061 (a different machine and R setup), so I kept the committed values; and 5
of the 9 weighted cases failed `test_vs_r`, with pyfixest p-values higher than R's (0.0190 vs 0.0084). Before
changing any code I read R's `ritest` source: every draw runs `fixest::demean(Xmat, fmat)` and `.lm.fit()` without
the weights, while the observed coefficient comes from the weighted fit. Emulating that in pyfixest reproduced R's
numbers (0.0095 vs 0.0084, 0.0047 vs 0.0047, 0.4908 vs 0.4910). So R's `ritest` is not a reference for weighted fits.
Decision: drop the weighted R rows (committing them would lock in R's behavior), keep the R script and CSV
unchanged, and explain it in the PR with the numbers.

**2026-11-04 (the baseline caught my own claim)**

`test-py` (6,643 passed in 28 min) failed one test: `tests/test_errors.py::test_ritest_error` asserted that a
weighted `feols` raises `NotImplementedError`, which is exactly the behavior this PR changes. Earlier I had said no
test checked that error, because the search used the full message and this test matches only "Weights are not
supported". It now asserts that `fweights` still raise, committed as `c3e2ad77`. Lesson: search for the shortest
distinctive part of a message, not the whole sentence.

**2026-11-09 (the base moved: merge instead of rebase)**

The PR monitor reported the PR as `CONFLICTING`. Upstream had merged 14 commits, including the 0.70.0 release
(`829940fc`). They touched three of my five files, but `git merge-tree` showed only one conflict: `docs/changelog.qmd`,
where my entry sat under 0.70.0, which had been restructured into release notes and shipped. `ritest.py` and
`test_ritest.py` were unchanged upstream, and the `_closed_form_ols` attribute my guard uses still exists after
0.70.0 removed most private attributes. The monitor drafted a rebase plus a force-push, but pyfixest's
`git-and-pr-style.md` says an agent rewrites history only with explicit approval, so I merged `upstream/master`
instead, taking its changelog, and moved the entry to a new `## PyFixest 0.80.0 (In Development)` section in its own
commit. The version number is my guess from the pattern 0.50.0, 0.60.0, 0.70.0, and a maintainer may rename it.
Before pushing I re-ran my manifest's first table: the changed-file lint passed, `tests/test_ritest.py` 97 passed,
`ty` passed and `test-py` 6764 passed (3822 skipped, 7 xfailed, 17 min). The first try of the last three failed in
seconds, and not because of the code: the shell still had another project's `VIRTUAL_ENV` active, so maturin refused
to build pyfixest and `ty` read the wrong packages. They passed once it was unset. The build also bumped
`pyfixest_core` in `Cargo.lock` to 0.70.0 (upstream's lockfile lags its `Cargo.toml`), and I restored it instead of
committing it. The push was a plain fast-forward (`c3e2ad77..efbf3dc0`), and GitHub now reports the PR as
`MERGEABLE`.

**Toolkit**

- Tool: Claude Code building on `feat/ritest-wls`, with git, pixi and the repo's pre-commit hooks (`prek`), and my
  `CLAUDE.local.md` for the branch and commit rules.
- What it produced: three commits, `aff179a1` (fix plus tests), `ab245941` (changelog) and `c3e2ad77` (the updated
  error test), each in the repo's `type(scope): summary` form.
- Difficulties: `pixi install` rewrote `pixi.lock`; `core.autocrlf=true` gave mixed line endings that the
  `mixed-line-ending` hook caught; `prek --from-ref` stashed and lost my uncommitted test edits while Rscript held a
  file open; and my claim that no test checked the old error was wrong.
- What changed: `--frozen` on every pixi command, a copy of edited files before any stash-based tool, the
  changed-file lint only on a clean tree, and searching for the shortest distinctive part of an error message.

---

## Pull Request

**PR Link:** https://github.com/py-econometrics/pyfixest/pull/2024 (draft, opened 2026-11-04 03:43 CST,
`VincentTLe:feat/ritest-wls` into `master`, head c3e2ad77; head efbf3dc0 after `master` was merged in on
2026-11-09)

**PR Description:** It follows the repo's template (Summary, Verification) and opens with the before and after:
`NotImplementedError` on `master`, `Pr(>|t|)=0.0050` on the branch, `Fixes #463`. It explains why removing the guard
wasn't enough (fast draws off by up to 0.28), lists the five changes (`_run_ri` scaling, the narrowed guard, two new
tests, the updated error test, the changelog), and says what still raises (`fweights`, weighted `fepois`/`feglm`).
It reports that R's `ritest` ignores weights in its draws, with the four emulated p-values, so there are no weighted R
rows, and offers a weighted `fixest::feols` permutation loop as a reference if the maintainers want one. It names
draft #2014 and promises a rebase if that lands first. Verification: `tests/test_ritest.py` 97 passed, `test-py`
6644 passed, `ty` and the changed-file hooks passed; the release contract skipped (no win-64 baseline) and the R
suites were left to CI. AI line: "This PR was written with Claude Code: it read the `ritest` code, ran the
reproduction and the checks, and drafted the change, the tests and this description. It's opened as a draft, per
the repo's rule for agent-authored work."

**Maintainer Feedback:**

- 2026-11-04: none yet (the PR was a few hours old).
- 2026-11-09: none yet, 5 days after opening: no comment, no review. Nothing to answer, but the branch needed work,
  because 0.70.0 shipped on 2026-11-08 and GitHub marked the PR as conflicting. I merged `master`, moved my changelog
  entry to 0.80.0 and pushed (Implementation Notes, 2026-11-09). My playbook's first nudge waits for 7 quiet days
  (2026-11-11).

**Status:** Awaiting review

**Toolkit**

- Tool: the ship-gate (my manifest plus my pr-precheck), run by hand with `--branch` and then as the hook on
  `gh pr create`, together with the voice guard.
- What it produced: run 1 stopped at check 3/4 (`mixed-line-ending` on four files). Run 2 (03:39) passed all four
  checks (299 s, 2 s, 5 s, 906 s) and pr-precheck accepted 10 of 10. At 03:42 the hook honored that pass by
  fingerprint (head, title, body, plan, evidence) and the voice guard passed the body.
- Difficulties: the line endings; `git push` failed twice with GitHub's "Internal Server Error" and `gh pr create`
  twice with a GraphQL "Something went wrong", so the PR was opened through the REST API, which the gate doesn't
  watch. pr-precheck also flagged that `test_evidence.md` still had a placeholder for the check output, and that my
  rubric's branch rule (`<type>/<issue-number>-<slug>`) conflicts with the scope's "the repo's conventions win".
  After the PR, the gate also stopped one of Claude's ordinary commands, a script that only edited this story,
  because the text held the words `gh pr create` and an apostrophe the gate couldn't split.
- What changed: `core.autocrlf=input` for the clone, the check output filled into `test_evidence.md`. I would add
  `gh api .../pulls` to the commands the gate watches, change the rubric's branch rule to defer to the repo's own
  convention, and make the gate's fallback match only a command that starts with `gh pr create`, not any text that
  contains it.

---

## Learnings and Reflections

**Technical Skills Gained**

- Weighted least squares as ordinary least squares on data scaled by `sqrt(w)`, and why a fast path that reuses
  precomputed matrices has to apply that scaling itself (`_run_ri` solved on `within_data`, which `fit_ols` never
  scales).
- Proving a numerical change without a trusted reference: make two independent algorithms agree on the same draws
  (3.44e-15 after the fix, 0.28 before it), and write a test that fails without the fix (6 of 6) before trusting it.
- Checking a reference before using it: reading R `ritest`'s source showed its draws ignore regression weights, and
  emulating that in Python reproduced its p-values. That one finding decided my test plan.
- Repo tooling on Windows: pixi environments and `--frozen` lockfiles, `core.autocrlf=input` against a `--fix=lf`
  line-ending hook, running prek on changed files only, and Claude Code hooks that see the Bash tool but not
  PowerShell until the matcher says so.
- Keeping a PR current: the difference between merging the base into a branch and rebasing it, why a rebase needs a
  force-push, and why a squash-merging repo makes the merge the safe choice.

**Challenges Overcome**

- The environment took longer than the code: pixi missing, `pixi install` rewriting `pixi.lock`, and R's `mvtnorm`
  failing to compile with Fortran. Each had a narrow fix (winget, `git checkout pixi.lock` plus `--frozen`, prebuilt
  conda-forge packages).
- Five of nine weighted R reference cases failed against my branch. The tempting move was to change my code until it
  matched R; reading R's source first showed that R was the one ignoring the weights, so I dropped the rows and
  explained why in the PR.
- My own claim that no test checked the old error was wrong, and `test-py` caught it
  (`tests/test_errors.py::test_ritest_error`). I fixed the test in its own commit and changed how I search.
- Every course hook assumed Claude Code's Bash tool, while on Windows it often runs PowerShell, so the voice guard and
  the ship-gate would have let commands through unchecked. Both were patched and tested before anything was posted.
- The base moved under my PR: 0.70.0 was released and my branch conflicted with `master`. The course plan said
  rebase and force-push, the repo's rules say an agent may not rewrite history without explicit approval for it, so
  I merged `master` instead.

**What I'd Do Differently Next Time**

- Spread the work out. I claimed #463 on 2026-10-27 and did the reproduction, plan, fix, tests and PR in one night
  (2026-11-04), so the report was a day late and every hard problem hit at once. Next time the environment and the
  reproduction start the day after the claim.
- Write the repo memory (`CLAUDE.local.md`) first. Mine was written last, after the sessions that needed it.
- Before planning tests against a reference implementation, check that it supports the feature at all.
- Measure the repo's rhythm before opening, including open PRs and not only merged ones, so a quiet week is
  expected rather than worrying.
- Walk through my own diff line by line before opening the PR, so I can answer a reviewer about `_run_ri` without
  help. Claude Code drafted most of the code, and I'm the one who answers for it.

---

## Resources Used

- pyfixest `docs/developer/git-and-pr-style.md`: the draft-PR rule for agent work, branch and commit names, and the
  rule that an agent rewrites history only with explicit approval, which made me merge `master` instead of rebasing.
- pyfixest `docs/developer/testing.md`: the selection matrix that set my manifest, and how to report checks.
- The `fit_ols` docstring in `pyfixest/estimation/internals/fit_.py` ("Demeaned but not WLS-transformed"), which
  explained the fast-path bug.
- R `ritest` 0.1.0 source (`ritest:::ritest`, deparsed in R), which showed that its draws ignore weights.
- The maintainer's to-do list in https://github.com/py-econometrics/pyfixest/issues/463, and draft PR
  https://github.com/py-econometrics/pyfixest/pull/2014 for the overlap.
- `.pre-commit-config.yaml` (`mixed-line-ending` with `--fix=lf`), which explained the first ship-gate stop.
- Five merged PRs from outside contributors, which set my playbook's staleness threshold:
  https://github.com/py-econometrics/pyfixest/pull/2025, https://github.com/py-econometrics/pyfixest/pull/1935,
  https://github.com/py-econometrics/pyfixest/pull/1884, https://github.com/py-econometrics/pyfixest/pull/1829 and
  https://github.com/py-econometrics/pyfixest/pull/1713.
- pyfixest commit e3d3a925 ("update changelog", #1353), which showed how the maintainer opens the next version's
  section, "0.70.0 (In Development)", the form I used for 0.80.0.
- The Unit 8 monitor's `INSTALL.md` (sections 2 and 6) for reading `CONFLICTING` and `DIRTY`, and the rebase steps.
