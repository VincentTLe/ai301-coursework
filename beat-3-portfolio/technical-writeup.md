# [YOUR TITLE: say what you did, e.g. the feature + the library]

<!-- SCAFFOLD. Every line starting with ">" is a note for you: turn its facts into your own sentences, then
delete the note. The fenced blocks are real proof from your record (Claude gathered them, which the assignment
allows). Keep them, and write the sentence next to each one that says what it proves. -->

## Overview

> One short paragraph, 4 to 6 sentences. Facts, in order:
> - pyfixest = open-source Python library for fixed-effects regressions (economics / econometrics).
> - Issue #463: `ritest()` (randomization inference) raises `NotImplementedError` on any weighted `feols`.
> - Your part: claimed it 2026-10-27, reproduced it 2026-11-04, found that deleting the guard alone gives wrong
>   draws on the fast path, wrote the `sqrt(weights)` fix in `_run_ri` plus 2 new tests, and opened draft PR #2024
>   on 2026-11-04: https://github.com/py-econometrics/pyfixest/pull/2024
> - How it stands TODAY (checked 2026-11-18): still open, still a draft, 0 comments and 0 reviews in 14 days,
>   and GitHub shows it CONFLICTING with `master` again (it was mergeable after your 11-09 merge).
> - Say "opened a fix", never "fixed".

## What happened

> Tell it in this order. Each ### is optional, but it keeps the order easy to follow.

### Claiming and reproducing the bug

> - 2026-10-27: claim comment on #463 (asked whether it was still open and fast path vs slow path).
> - 2026-11-04: reproduction on `master` 2aba80fd, a day later than the week you promised (you said so).
> - Unweighted fit = control, runs fine. Weighted fit stops at the guard. PROOF 1 below.

```
weights=None: Pr(>|t|)=0.0000
weights=weights: NotImplementedError: Regression Weights are not supported with Randomization Inference.
```

> - Then you deleted ONLY the guard and ran slow vs fast on the same 200 draws. They disagreed by up to 0.28,
>   and with `| f1` the p-value was wrong (fast 0.0000 vs slow 0.0050). The no-FE row shows matching p-values
>   hiding different draws, so checking p-values alone wasn't enough. PROOF 2:

```
Y ~ X1 + f3       slow Pr(>|t|)=0.0151  fast Pr(>|t|)=0.0151  max |slow - fast|: 2.10e-01
Y ~ X1 + f3 | f1  slow Pr(>|t|)=0.0050  fast Pr(>|t|)=0.0000  max |slow - fast|: 2.82e-01
```

> - Why (explain in your own words, you must be able to say this at Demo Day): the slow path refits a real
>   weighted model each draw. The fast path `_run_ri` demeans with the weights but then solves with
>   `lstsq_numba` on data that was never `sqrt(w)`-transformed, so it does an unweighted solve.

### The fix

> - Multiply Y, the other regressors, and each permuted treatment by `sqrt(weights)` before the solve, the same
>   way `fit_ols` does. Unit weights change nothing, so unweighted fits are unaffected. PROOF 3 (your diff):

```diff
-    X_demean2 = np.ascontiguousarray(X_demean2)
+    # The demeaned data are not WLS-transformed, so apply the square-root
+    # weights here, as `fit_ols` does. Unit weights leave the data unchanged.
+    sqrt_weights = np.sqrt(weights).reshape((-1, 1))
+    X_solver = np.ascontiguousarray(X_demean2 * sqrt_weights)
+    Y_solver = Y_demean * sqrt_weights.ravel()
 ...
         ri_coefs[i] = lstsq_numba(
-            np.concatenate((D2_demean, X_demean2), axis=1), Y_demean
+            np.concatenate((D2_demean * sqrt_weights, X_solver), axis=1), Y_solver
         )[0]
```

> - The guard in `Feols.ritest()` was narrowed, not removed: `fweights` and weighted non-OLS (`fepois`) still raise.
> - Result: max slow-vs-fast difference dropped from 0.28 to 3.44e-15, and the `| f1` p-values both became 0.0050.
> - New test `test_wls_algos_internally` (no FE, `|f1`, `|f1+f2`, with and without cluster) asserts slow == fast
>   to 1e-8. With the `_run_ri` change stashed it fails 6 of 6. PROOF 4:

```
FAILED tests/test_ritest.py::test_wls_algos_internally[X1-None-Y~X1+D_cluster]
FAILED tests/test_ritest.py::test_wls_algos_internally[X1-None-Y~X1+D_cluster|f1]
FAILED tests/test_ritest.py::test_wls_algos_internally[X1-None-Y~X1+D_cluster|f1+f2]
FAILED tests/test_ritest.py::test_wls_algos_internally[D_cluster-group_id-Y~X1+D_cluster]
FAILED tests/test_ritest.py::test_wls_algos_internally[D_cluster-group_id-Y~X1+D_cluster|f1]
FAILED tests/test_ritest.py::test_wls_algos_internally[D_cluster-group_id-Y~X1+D_cluster|f1+f2]
6 failed, 91 deselected in 92.38s (0:01:32)
```

### What I changed when the plan fell short

> THIS IS YOUR "change you made" (10 pts). A second version, not a first one.
> - First version of the plan, step 5: add weighted cases to the R comparison (R `ritest`) as reference values.
> - What happened (2026-11-04): you generated the rows (R 4.4.3, fixest 0.14.0, ritest 0.1.0), and 5 of 9 failed,
>   with pyfixest p-values systematically higher (0.0190 vs 0.0084).
> - You read R's `ritest` source: every draw runs `fixest::demean()` and `.lm.fit()` WITHOUT the weights, while the
>   observed coefficient comes from the weighted fit. Emulating that in Python reproduced R's numbers:
>   0.0095 vs 0.0084, 0.0047 vs 0.0047, 0.4908 vs 0.4910, 0.5060 vs 0.4981.
> - What you changed: dropped step 5, so committing those rows would not lock in R's behavior. The weighted check
>   became the slow-vs-fast test (the slow path is pyfixest's normal WLS fit), and the PR explains this and offers
>   a weighted `fixest::feols` permutation loop if maintainers want an R reference. PROOF 5 (plan before/after):

```
5. R reference values: add weighted `feols` cases to `tests/r_test_comparisons.R`, add their rows (and a
   `weights` column) to `tests/data/ritest_results.csv`, and extend `test_vs_r` to compare them.
   **Changed 2026-11-04 (dropped):** the rows were generated (R 4.4.3, fixest 0.14.0, ritest 0.1.0), but
   5 of 9 failed against this branch, with pyfixest p-values systematically higher (0.0190 vs 0.0084).
```

> - (Optional second, smaller change: `test-py` failed `tests/test_errors.py::test_ritest_error`, which asserted the
>   old "weights always raise" behavior. You changed it to check that `fweights` still raise.)

### Opening the PR, and keeping it alive

> - 2026-11-04: checks from your ship manifest: `tests/test_ritest.py` 97 passed, `test-py` 6644 passed, `ty`
>   passed, and the pre-commit hooks on changed files passed. Opened as a DRAFT because pyfixest asks for drafts on
>   agent-authored work, and the description says Claude Code helped.
> - 2026-11-08: pyfixest 0.70.0 shipped, and your PR went CONFLICTING (only `docs/changelog.qmd`, because your
>   entry sat under the released 0.70.0).
> - 2026-11-09: your PR monitor flagged it and drafted a REBASE + force-push. You merged `master` instead, because
>   pyfixest's `git-and-pr-style.md` lets an agent rewrite history only with explicit approval (and the repo
>   squash-merges anyway). Moved the entry to 0.80.0, re-ran all 4 checks (`test-py` 6764 passed), pushed `efbf3dc0`.

### Where it stands

> THIS IS YOUR "result that landed" (10 pts). Be honest: no maintainer response landed. What DID land because of
> your work: the merge took the PR from conflicting back to mergeable (monitor log below). PROOF 6:

```
## 2026-11-09 22:21 [run] https://github.com/py-econometrics/pyfixest/pull/2024
- state: branch-stale: the branch needs work: merge conflicts with the base
- mergeability: CONFLICTING/DIRTY

## 2026-11-09 22:53 [state] https://github.com/py-econometrics/pyfixest/pull/2024
- state: quiet: the thread is quiet: 5 days since the last activity by anyone but you
- mergeability: MERGEABLE/UNSTABLE
```

> - Today (2026-11-18): still open as a draft, 0 comments, 0 reviews, and CONFLICTING again since `master` moved.
>   Your playbook allowed one nudge from 2026-11-11, and none has been posted yet. Say that plainly.

## Limitations and what's next

> A real limit + a next step that follows from it. Pick 1-2 honest ones:
> - No maintainer has reviewed it, so it is unknown whether they accept the approach (aweights only, the test
>   design). Next: merge `master` again to clear the new conflict, then post your one nudge (tag only s3alfisc)
>   with something new in it.
> - Only `aweights` + `feols`: `fweights` and weighted `fepois`/`feglm` still raise. Next: those need their own
>   design (permuting a frequency-weighted row moves all its copies together).
> - No external weighted reference: R's `ritest` ignores weights, so correctness rests on slow == fast. Next:
>   write the weighted `fixest::feols` permutation loop in R as an independent check.
> - (Process) The PR was opened through the REST API after `gh pr create` failed twice, so that call skipped the
>   ship-gate hook. Next: add `gh api .../pulls` to the commands the gate watches.
