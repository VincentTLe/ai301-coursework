# Evidence guide: where proof lives in a reproduction package

In eval mode the bundle is the whole world: the `## Repo facts` block,
the `## Issue` section (title, opener, body), `## Thread highlights`,
`## Candidate claim comment`, and `## Candidate repro report`. In live
mode the issue side comes from GitHub (`gh issue view <n> -R <repo>
--comments`, and the repo's `docs/CONTRIBUTING.md`, README, and any
`AI_POLICY.md`/`AGENTS.md`), and the candidate side is the student's
draft file(s), read as a stranger on the thread would read them: only
what the drafts contain or quote counts.

## Environment

- Where it lives: the repro report's first lines, usually an
  `Environment:` line or block. Read it against the issue body (the
  version the reporter used, and any platform, driver, backend, shell,
  build profile, or language setting the issue ties the bug to), the
  thread highlights (maintainer notes such as "only on Windows", "could
  not reproduce on the Store build", "confirmed on main"), and the
  repo-facts `bug reports:` line (what the template asks for) and
  `latest release:` line. Live: the report draft's environment section,
  against the issue body and the repo's setup docs (Python version,
  dependency install command, commit checked out).
- What good looks like: OS/platform, the exact version or commit under
  test, and every setting the issue says the failure depends on are all
  named. If the tested version or platform differs from the issue's,
  the report says so in words. An old version tested against an issue
  confirmed on latest, with no mention of the gap, is a deviation, not
  a record.

## Steps

- Where it lives: the repro report's `Steps:` section and any code
  blocks, input files, configs, or links it contains. Read against the
  issue's own steps and any maintainer note naming the required trigger
  (flag, syntax, input shape). Live: the report draft's steps, which
  for Path Review should start from a fresh clone of the student's fork
  or the upstream repo at a named commit.
- What good looks like: from a clean starting state, a stranger can
  type or paste each step and reach the trigger using only what is
  shown or publicly linked. The trigger itself is present and
  unmodified (the same range syntax, operator, flag, or input as the
  issue). Private repos, unshared configs, and prose like "set up the
  project" are gaps. Brevity is not a gap, and neither is an input
  that is described rather than pasted when the description (or the
  issue it points back to) pins down everything the trigger needs.

## Behavior shown

- Where it lives: fenced code blocks and quoted output in the repro
  report (terminal transcripts, console output, tracebacks, log
  excerpts, produced files), plus the report's `Expected:`/`Actual:`
  lines. Read each artifact against the issue's described symptom in
  the issue body and maintainer comments. Live: the output pasted into
  the report draft (for Path Review, the pytest output or traceback).
- What good looks like: the artifact contains the issue's own symptom
  (same exception type and message, same exit code or panic, same wrong
  value or line numbers, same crash-versus-graceful outcome), produced
  by the issue's trigger. An adjacent failure is a miss even when the
  narration says "confirmed": a syntax, validation, or compile error
  where the issue reports a crash or panic; a different exception;
  garbled output while the program keeps running where the issue
  reports a crash; output that only shows the program starts. A
  control run (same steps without the trigger, output shown) is the
  strongest version. A cannot-reproduce shows the attempted trigger and
  the non-failing output.

## Honesty

- Where it lives: every assertion in the claim comment and the report
  ("reproduced", "confirmed", "verified", "root cause is",
  "guaranteed", "on all platforms", "same as the issue"), each set next
  to the artifact that should back it.
- What good looks like: each claim is no bigger than its artifact.
  "Reproduced" has an artifact showing the symptom; a cause is either
  shown (a trace, a code pointer with evidence) or labeled as a
  hypothesis; the scope of the result matches the environments actually
  tested; `Expected:` and `Actual:` agree with what the artifact shows.
  An honest cannot-reproduce says so first, shows the attempt, and
  names what may differ from the reporter's setup; that is a complete
  report, not a failed one. Confident wording over missing or mismatched
  artifacts is the failure this family exists to catch.

## Comms

- Where it lives: the claim comment, read against the issue's title,
  body, and thread; both comments, read against the repo-facts
  `contribution policy` line (including any AI policy file it names) and
  the `bug reports:` template asks. Live: the draft(s), against the
  issue thread and the repo's `docs/CONTRIBUTING.md` and any AI policy
  file; also `scope.md`'s house rules for Path Review.
- What good looks like: the claim comment names this issue's specifics
  (the symptom, a file, function, or scenario) and a next step that is
  an investigation or a report back; it does not ask to be assigned,
  ask for the issue to be reserved, compliment the project in place of
  content, or promise a fix, a PR, or a date. For AI policy, sort the
  policy line into one of three kinds: (1) disclosure required for
  comments, issues, or "all AI usage": at least one of the two comments
  must say which AI tool was used and for what; (2) comments must be
  human-written in the author's own words: first-person writing about
  the author's own run satisfies it; (3) no policy, or conditions on
  code/PRs only (understand it, test it, review it, disclose in the
  PR): no disclosure needed in comments. Treat every package as
  AI-assisted when applying kind (1).
