# Adding Weighted Randomization Inference to pyfixest's ritest()

## Overview

I worked on pyfixest issue #463, which concerns an open-source Python library for statistical regression. Its `ritest()` method could not run randomization inference on a weighted `feols` regression: instead, it raised `NotImplementedError`. I reproduced the failure, found that simply removing the error would leave the fast algorithm calculating incorrect draws, and opened a fix that applies weighted least squares (WLS) consistently in the fast algorithm. I also added regression tests and documented why I did not use the existing R implementation as a weighted reference. My [draft PR #2024](https://github.com/py-econometrics/pyfixest/pull/2024) remains open as of November 18, 2026, without a maintainer review, and GitHub currently reports a merge conflict.

## What happened

### October 27–November 4: Reproducing the failure

On October 27, I commented on issue #463 to claim the work and ask whether the expected scope included both the fast and slow algorithms. I planned to reproduce the issue before changing anything. I posted my reproduction on November 4, one day later than the week I had promised. On the project's master branch at commit 2aba80fd, I ran the same model with and without regression weights. The actual output from that investigation was:

```
weights=None: Pr(>|t|)=0.0000
weights=weights: NotImplementedError: Regression Weights are not supported with Randomization Inference.
```

The unweighted case worked, but the weighted case stopped at a guard inside `Feols.ritest()`. I initially tested what would happen if I removed only that guard. That exposed a second problem: using the same random seed and 200 draws, the slow and fast algorithms produced different permuted coefficients, even when their final p-values sometimes agreed.

```
Y ~ X1 + f3       slow Pr(>|t|)=0.0151  fast Pr(>|t|)=0.0151  max |slow - fast|: 2.10e-01
Y ~ X1 + f3 | f1  slow Pr(>|t|)=0.0050  fast Pr(>|t|)=0.0000  max |slow - fast|: 2.82e-01
```

This was important because matching p-values alone would not have detected the difference in the first row. The slow algorithm refits the regression for every permutation through `refit()`, preserving its weights. The fast algorithm uses Numba and solves with `lstsq_numba`, but its demeaned data had not yet been scaled for weighted least squares. Removing the guard alone would therefore allow the function to run while potentially returning incorrect results.

### November 4: Implementing and testing the change

I changed `_run_ri` to multiply the response, the other regressors, and each permuted treatment by the square roots of their regression weights before solving. This mirrors the transformation already used in `fit_ols`. Here is an excerpt of the actual PR diff:

```diff
-    X_demean2 = np.ascontiguousarray(X_demean2)
+    sqrt_weights = np.sqrt(weights).reshape((-1, 1))
+    X_solver = np.ascontiguousarray(X_demean2 * sqrt_weights)
+    Y_solver = Y_demean * sqrt_weights.ravel()
 ...
-            np.concatenate((D2_demean, X_demean2), axis=1), Y_demean
+            np.concatenate((D2_demean * sqrt_weights, X_solver), axis=1), Y_solver
```

With the correction, the maximum difference between fast and slow draws dropped to 3.44e-15 in the reported check, and both paths returned 0.0050 for the fixed-effects example. I added `test_wls_algos_internally`, covering three regression specifications with and without clustering, and `test_ritest_rejects_unsupported_weights`. The former compares the actual draw arrays to a tolerance of 1e-8; without the `_run_ri` correction, all six weighted cases failed. I kept frequency weights (`fweights`) and weighted non-OLS estimators unsupported rather than claiming those cases were validated.

### November 4: Revising my original test plan

My initial plan also included step 5: adding weighted outputs from R's `ritest` to the project's comparison tests. I generated nine candidate comparisons, but five failed against the Python branch; one comparison gave 0.0190 in pyfixest versus 0.0084 in R. Instead of adjusting the Python implementation merely to match those values, I inspected the R implementation. Its permutation loop used `fixest::demean()` and `.lm.fit()` without applying the regression weights, although the observed coefficient came from the weighted fit. Reproducing that combination in Python gave numbers close to R's results.

That finding changed my plan. I dropped step 5 rather than commit a weighted reference that did not actually perform weighted refits. I left `r_test_comparisons.R` and its CSV unchanged, and used the existing slow weighted-refit path to check the fast algorithm. This is a useful internal consistency check, but not an independent external validation, a limitation I described in the PR.

### November 4–18: Opening and maintaining the draft PR

After running the targeted `ritest` tests (97 passed), the Python suite (6,644 passed), type checking, and changed-file hooks, I opened draft PR #2024 on November 4. I disclosed that Claude Code had helped inspect the code, run checks, and draft changes, and kept the PR in draft form under the project's rule for agent-assisted contributions.

When pyfixest released version 0.70.0, the PR conflicted with the updated changelog. On November 9, my PR-monitoring workflow recommended a rebase and force-push. I instead merged master into the branch to avoid rewriting history, moved my changelog entry into a proposed 0.80.0 development section, and reran the checks (including 6,764 passing Python tests). The monitoring log records a concrete result of that maintenance work:

```
2026-11-09 22:21: mergeability: CONFLICTING/DIRTY
2026-11-09 22:53: mergeability: MERGEABLE/UNSTABLE
```

That change restored GitHub's reported mergeability at the time; it did not merge the feature into pyfixest. As of November 18, the upstream branch has moved again, the draft PR is conflicting again, and no maintainer has commented or reviewed it.

## Limitations and what's next

The biggest limitation is that fast-versus-slow agreement does not establish correctness against an independent weighted implementation. My change also covers only `feols` with analytic weights (`aweights`), not frequency weights or weighted nonlinear models. I would first write a separate R comparison that explicitly refits a weighted `fixest::feols` model on each permutation. I also need to merge the latest master into my branch, rerun the tests, and then send one concise follow-up asking for feedback on the algorithm and test coverage. Until a maintainer reviews and accepts the PR, this remains a proposed fix rather than a merged feature.
