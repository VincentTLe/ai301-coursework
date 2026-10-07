# Rubric: is this plan ready to post and build from?

## How to read the package

"The repro evidence" is the accepted reproduction the plan builds on:
its environment, numbered steps, artifacts, control runs, and
Expected/Actual lines. A "control" is any step that changes one thing
(a flag removed, a version swapped, a component bypassed, a cache
cleared) and shows what happens. Controls are the strongest facts in
the package: they say what the cause is NOT.

"Maintainer direction" is an explicit statement in the thread
highlights by someone marked OWNER, MEMBER, COLLABORATOR, or
CONTRIBUTOR on the issue's repo that names a culprit or fix site,
proposes or rejects an approach, posts a patch or test build, or asks
for something to be tested. "Prior art" is an open or proposed pull
request or patch for this issue named in the thread.

When the thread and the repro evidence disagree about the cause, the
repro evidence wins.

In live mode, the Path Review house rules in `scope.md` apply: a
classmate's plan on the same issue is never a reason to fail a check,
but a comment that only points at a classmate's plan ("same approach
as above") is not a plan.

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| `diagnosis-grounded` | The cause the candidate plan states (its Diagnosis or Cause line, or the first sentence of the plan if it has no heading), read against every step, control, and Expected/Actual line in the repro evidence. | Pass if the plan names a specific cause (a component, function, value, or code path) AND that cause is consistent with every control and step in the repro evidence. Fail if the plan names no cause ("clearly has problems", "figure out what happened"); if any control or step shows the named cause cannot be it (the supposedly missing or broken part works in a control, the damage is already present before the blamed step runs, the blamed component behaves correctly when exercised alone); or if the plan waves away a piece of repro evidence ("red herring", "side effect") without showing why. Adopting the issue's or thread's diagnosis passes only if the repro evidence does not contradict it. | required |
| `fix-at-cause` | The plan's change (Change, Approach, or Proposed changes), read against where the repro evidence shows the fault first appears. | Pass if the change acts at the point where the stated, grounded cause sits, so the reproduced behavior can no longer happen. Fail if the change only hides or works around the behavior while the evidence locates the fault elsewhere: a documentation-only workaround for a code defect, catching or suppressing the error, adding retries, or repairing already-damaged output downstream of where the data was lost. Clamping, validating, or handling the bad value at the site where it is produced or consumed counts as fixing at the cause. | required |
| `scope-bounded` | The plan's list of changes, its in-scope and not-in-scope statements, and its files, read against the issue's reported behavior. | Pass if every change in the plan is needed to fix the reproduced behavior or to test it: the fix, the same fix applied to sibling sites of the same defect, and regression tests. Explicitly deferred work listed as not in scope is fine. Fail if the plan also does work the issue never asked for: a dependency upgrade or migration, a rewrite or restructure of the surrounding module, a new option, setting, or prop, UI or behavior changes for other symptoms, new CI infrastructure, or "while in the area" fixes, even when the core fix is correct, even when they are framed as a PR series. | required |
| `executable` | The plan's files or areas and its approach steps. | Pass if a stranger could start work without asking the author anything: the plan names at least one file, function, or code area precise enough to open, AND it commits to one concrete change there. An exact line or function inside a named area may be left as a stated unknown. Fail if there is no file or code area; if the approach is investigation instead of a change ("profile", "look into", "try different X and see", "fix it once the cause is clear"); or if a real decision is left open between alternatives ("somewhere", "upstream or vendored, whichever is easier", "gocui? tcell? not sure"). | required |
| `test-decisive` | The plan's test plan, read against the repro evidence's steps and Expected line. | Pass if the test plan names an observable outcome that would differ between the broken and the fixed code: the repro steps or trigger re-run with the expected output, exit code, value, or visible behavior, or a named regression test that asserts the issue's case. Fail if the only success criteria are feelings or absences that the bug never touched ("should feel fast", "nothing else should feel broken", "should look much better"), or only "the existing/full test suite passes" with nothing tied to this fix. | required |
| `thread-engaged` | The candidate plan comment, read against the thread highlights (maintainer direction and prior art as defined above). | Pass if the thread has no maintainer direction and no prior art, or if the plan comment engages each piece that exists: it follows the direction, or names it and says why the plan departs, and it names any prior-art PR or patch and says how this plan relates to it. Fail if the comment ignores or silently contradicts maintainer direction (a named culprit file, a posted patch or test build, a proposed or rejected approach), or proposes a different change without acknowledging it. Fail a comment that only defers to someone else's plan ("same approach as above"). | required |
| `ai-disclosure` | The repo-facts `contribution policy` line (and any AI policy file it names), read against the text of the plan comment. Treat every package as AI-assisted work. | If the policy requires disclosure of AI use for comments, issues, or "all AI usage in any form", pass only if the plan comment discloses the AI assistance (the tool and what it was used for); fail if it does not, however good the plan. If the policy requires comments to be written by a human in their own words, pass if the comment reads as first-person writing by the author (a statement that it is in their own words is enough). Pass when there is no stated AI policy, or when the policy only asks for understanding, responsibility, review, testing, or disclosure in pull requests. | required |
| `unknowns-named` | The plan's risk, unknowns, or open-question lines, and any statement of certainty in the plan or comment. | Pass if the plan names at least one risk or unknown it has not verified (or states that it found none), and states nothing as verified that the package does not show. | preferred |
| `comment-matches-plan` | The plan comment, read against the plan. | Pass if the comment describes the same change and scope as the plan, with no extra promises the plan does not contain. | preferred |

## Verdict rule

- `accept` if and only if every `required` check grades `pass`.
- `reject` if any `required` check grades `fail` or `unclear`.
  `unclear` counts as fail: a plan the grader cannot verify from the
  package is not ready to build from. Use `unclear` only when the
  evidence the check needs is genuinely absent from the package, never
  as a softer fail.
- `preferred` checks are reported but never change the verdict.
- A plan that is long, confident, or polished gets no credit for that;
  a short plan that meets every pass condition is ready.
