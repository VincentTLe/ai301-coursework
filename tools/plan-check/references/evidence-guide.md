# Evidence guide: where evidence lives in a plan package

In eval mode the bundle is the whole world. Its parts, in file order:
`## Repo facts` (the `bug reports:` and `contribution policy` lines),
`## Issue` (title, opener, body), `## Thread highlights` (each line
starts with a date, a username, and the author's role in parentheses),
`## Repro evidence` (environment, steps, artifacts, controls,
Expected/Actual), `## Candidate plan`, and `## Candidate plan comment`.

In live mode the issue side comes from GitHub
(`gh issue view <n> -R <repo> --comments`, plus the repo's
`docs/CONTRIBUTING.md`, README, and any `AI_POLICY.md` or `AGENTS.md`).
The repro evidence is the student's own posted repro comment on that
issue (the comment by the student's GitHub user that contains the
environment, commands, and output), and whatever repro evidence the
drafts quote. The candidate side is `plan.md` and the draft comment
file, read as a maintainer on the thread would read them.

## Diagnosis and grounding

- Where it lives: the cause is in the plan's `Diagnosis:` or `Cause:`
  line or section; if there is no heading, it is the first sentence
  that says why the bug happens. The behavior the cause must explain
  is in the repro evidence: each numbered step, each `Control` line
  or block, the `--debug`/trace output, and the `Actual:` line. Live:
  the diagnosis section of `plan.md`, read against the student's
  posted repro comment (traceback frames, file and line numbers,
  control one-liners).
- What good looks like: the named cause is a specific thing (a
  function, file, constant, code path, or ordering) and every control
  is consistent with it. Test it by asking, for each control, "if this
  cause were true, would this control have come out the way it did?"
  If a control shows the blamed part working (the friendly-error
  system prints in the same build; the tokenizer parses the same items
  without the flag; the zeros are already gone before the cast runs),
  the diagnosis is contradicted, however confident it sounds. A plan
  that calls a repro result a "red herring" or "side effect" without
  evidence is ignoring the evidence. A plan that repeats the thread's
  theory is grounded only if the repro evidence does not rule it out.

## Scope

- Where it lives: the plan's `Scope`, `Change`, or `Proposed changes`
  section, its `In scope` / `Not in scope` lines, and its `Files` or
  `Files and areas` list. Compare against the issue body's reported
  behavior. Live: the scope and files sections of `plan.md`.
- What good looks like: one change aimed at the reported behavior,
  plus regression tests, plus (allowed) the same fix applied at
  sibling sites of the same defect pattern. A not-in-scope line that
  names tempting adjacent work (a bigger rework, another symptom from
  the thread, another platform) is a strength. A drive-by rewrite
  looks like a numbered list where the fix is one item among
  migrations, upgrades, new options or settings, module restructures,
  UI changes for other symptoms, retry frameworks, or new CI jobs.
  "It removes the whole class" and "while touching this" are the
  phrases to watch for.

## Executability

- Where it lives: the plan's `Files` list and its `Approach` or
  numbered steps. Live: the files and approach sections of `plan.md`.
- What good looks like: a named file path, function, or clearly
  bounded code area (for example "the client attach path in
  zellij-server's session connection handling"), and a stated change
  to make there (add `--` before the path, clamp with
  `saturating_sub`, wire the constant into the freshness check). A
  remaining unknown about the exact line, stated as an unknown, is
  fine. Not executable: steps that are verbs of search ("profile",
  "investigate", "look into", "try ... and see"), a fix site of
  "somewhere", or a choice left open between options to decide at
  build time.

## Test plan

- Where it lives: the plan's `Test plan` or `Test:` line, read against
  the repro evidence's steps and `Expected:` line. Live: the test plan
  section of `plan.md` against the steps in the student's posted repro
  comment.
- What good looks like: it re-runs the repro trigger and names what
  must be seen after the fix (an exit code, an output string, a
  returned value, a color flip, a passing named test that failed
  before), ideally with the controls still unchanged. A vague test
  plan names no outcome the bug would change: "feels fast", "nothing
  else feels broken", "timings look better", or only "the full suite
  passes".

## Honesty

- Where it lives: the plan's `Risk`, `Unknowns`, or `open question`
  lines; any `Deviations` section (live mode, after the build); and
  every certainty word in the plan and comment ("confirmed", "root
  cause is", "verified", "clearly").
- What good looks like: unverified things are named as unknowns with
  what would settle them (which layer clamps, whether every tool
  accepts `--`, the benchmark cost). "Risk: none identified" is an
  honest statement. False confidence is a cause or result stated as
  verified that the repro evidence does not show. A deviation found
  mid-build is honest when it is written under `## Deviations` in
  `plan.md` with what changed and why; a deviation that only shows in
  the diff is not.

## Comms

- Where it lives: the plan comment, read against two places. (1) The
  thread highlights: look for lines from OWNER, MEMBER, COLLABORATOR,
  or CONTRIBUTOR that name a culprit file, propose or reject an
  approach, post a patch or test binary, or ask for testing, and for
  any PR number someone opened for this issue. (2) The repo-facts
  `contribution policy` line, including any AI policy it names. Live:
  the issue thread on GitHub, the repo's CONTRIBUTING and AI policy
  files, and `scope.md`'s house rules.
- What good looks like: thread-aware comments name the direction they
  follow ("along the lines already agreed here", "the option 2 the
  collaborator suggested", "DHowett called this an invalidation bug")
  and name prior-art PRs with how they relate ("not racing #3314; I'll
  add tests to it if it lands"). A comment that proposes a different
  route than the one a maintainer gave, without mentioning it, or that
  ignores a maintainer's posted patch, is boilerplate however polite.
  For AI policy, sort the policy line into one of three kinds:
  (1) disclosure required for comments, issues, or "all AI usage": the
  plan comment must say which AI tool was used and for what;
  (2) comments must be in the author's own words: first-person writing
  (or a statement that it is in their own words) satisfies it;
  (3) no policy, or conditions only on code or PRs (understand it,
  test it, review it, disclose in the PR): nothing is needed in the
  comment. Treat every package as AI-assisted when applying kind (1).
