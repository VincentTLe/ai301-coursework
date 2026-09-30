# Rubric: is this reproduction package ready to post?

## How to read the package

"The issue's behavior" means the specific symptom the issue (and any
maintainer or owner comment in the thread highlights) describes: the
error type or message, the exit code, the wrong value, the crash versus
graceful failure, and the trigger that produces it (the input, flag,
syntax, or setting the issue or a maintainer says is required). An
"artifact" is output the author shows from their own run: a pasted
terminal transcript, console output, log excerpt, traceback, produced
file content, or a measured value. A sentence describing what happened
is not an artifact.

In live mode, the Path Review house rules in `scope.md` apply: a
classmate's earlier claim or repro on the same issue is never a reason
to fail a check here.

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| `env-recorded` | The repro report's environment record (see the evidence guide's Environment section), read against the issue body and the repo-facts `bug reports:` line for which settings the failure depends on. | Pass if the report names (a) the OS or platform, (b) the version of the software under test (release number, commit, or package version), and (c) any setting the issue or a maintainer says the failure depends on (driver, backend, build profile, shell, browser language, and so on). Fail if any of (a), (b), (c) is missing. A single terse line that names all three passes. | required |
| `env-faithful` | The version and platform in the report's environment record, read against the version/platform the issue targets and the thread's statements about where it does and does not occur. | Fail if the report tested a version or platform different from the one the issue targets (older than the reported/confirmed version, a different release channel, a different OS on an OS-specific issue) AND does not say so. Also fail if the report extends the bug to an environment it did not test, or to one the thread says does not reproduce. Pass if the environment matches, or every difference is stated in the report (for example "filed against 13.0.0; tested on 15.2.0"). | required |
| `steps-rerunnable` | The repro report's steps (see the Steps section): commands, code, input files, configs, and links, read from a clean starting state through to the trigger. | Pass if a stranger with only public resources could run the same attempt: the exact command that triggers the bug is shown, and every input that matters is either shown inline, linked publicly, taken from the issue itself ("the issue's input with rangeStart 19"), or specified precisely enough to rebuild in a minute ("an env.yml with a valid `dependencies:` list plus a `category:` section"). Fail if any step depends on something the reader cannot get (a private repo, an unshared config or file, "my project"), if a step is only a description with no command where one is needed ("set up the project", "run it with the usual flags"), or if the steps omit or change the trigger. Terse steps pass; an input that is described rather than pasted passes when the description pins down everything the trigger needs. | required |
| `behavior-matches-issue` | The report's artifacts (see the Behavior shown section), read line by line against the issue's behavior as defined above. | For a reproduction claim: pass only if an artifact shows the issue's behavior itself: the same error type/message or exit code, the same wrong value, the same crash-versus-graceful outcome, produced by the issue's trigger. Fail if there is no artifact, if the artifacts only show that the software runs, or if the artifact shows a different failure (a syntax/validation/compile error, a different exception, a graceful error where the issue reports a crash, garbled output where the issue reports a crash), however it is narrated. For a cannot-reproduce report: pass if artifacts show the attempt with the issue's trigger and its non-failing result. | required |
| `claims-backed` | Every assertion in the claim comment and the report ("I reproduced", "confirmed", "the cause is", "verified", "guaranteed", "on every platform", "expected/actual"), each read against the artifacts shown. | Fail if any assertion claims more than the artifacts show: reproduction stated without an artifact that shows it; a root cause stated as verified with no shown evidence; certainty ("guaranteed reproducible", "confirmed on two machines") with nothing shown; a result generalized beyond what was tested; or an expected/actual statement that contradicts the artifact. A cannot-reproduce passes when it says plainly it did not reproduce, shows the attempt, and names what may differ from the reporter's conditions. Hypotheses labeled as hypotheses pass. This check reads the central claims (that the bug reproduces, what causes it, where it occurs); a secondary observation stated in prose next to a shown main artifact (a control run or a variation tried, without its output pasted) is not by itself an overclaim. | required |
| `claim-specific` | The claim comment, read against the issue title, body, and thread. | Pass if the claim comment names something specific to this issue (the symptom, a file, function, command, or scenario from the issue or thread) AND states a concrete next step the author will take, framed as investigation or a report back. Fail if the comment would read the same on any other issue (compliments, "assign me", "please reserve this"), if it is a "+1"/"same here" with no intent, or if it promises a fix, a PR, or a date ("fixed within 2 days", "guaranteed"). | required |
| `ai-disclosure` | The repo-facts `contribution policy` line (and any AI policy file it names), read against the text of the claim comment and the repro report. Treat every package as AI-assisted work (course packages are). | If the policy requires disclosure of AI use for comments, issues, or "all AI usage in any form", pass only if the claim comment or the report discloses the AI assistance (the tool and what it was used for); fail if neither does, however good the proof. If the policy requires comments to be written by a human in their own words, pass if the comments read as first-person writing about the author's own run. Pass when there is no stated AI policy, or when the policy only asks for understanding, responsibility, human review, testing, or disclosure in pull requests (not in comments). | required |
| `control-run` | The report's artifacts. | Pass if the report shows a control run that isolates the trigger (the same steps without the trigger, or with the working variant, and its output). | preferred |
| `template-asks-covered` | The repo-facts `bug reports:` line (the template's asks), read against the report. | Pass if the report supplies every field the repo's bug-report template asks for, or the repo states no template. | preferred |

## Claim-only drafts (live mode)

For a claim-only draft, only `claim-specific`, `ai-disclosure`, and
`claims-backed` apply; the rest grade `unclear` with evidence `not yet
applicable: claim-only draft` and are left out of the verdict.
`claims-backed` then reads the claim comment alone: a claim-only draft
has no report behind it, so it fails if it states that the bug is
reproduced, confirmed, or caused by something. A claim posted before
the reproduction promises the investigation; it does not assert the
result.

## Verdict rule

- `accept` if and only if every `required` check that applies grades
  `pass`.
- `reject` if any applicable `required` check grades `fail` or
  `unclear`. `unclear` counts as fail: proof the grader cannot verify is
  not ready to post.
- `preferred` checks are reported but never change the verdict.
- Checks reported as `not yet applicable: claim-only draft` are left out
  of the verdict.
