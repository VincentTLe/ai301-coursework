# Evidence guide: where evidence lives in a PR package

**An eval bundle**, in file order, has these parts:
- `## Repo facts`: its `pull requests:` line holds the PR template's
  sections, checklist items, and required links and entries, and its
  `contribution policy` line holds the contribution rules and any AI
  policy.
- `## Issue`: the reported behavior.
- `## Thread highlights`: each line has a date, a username, and a role.
- `## Plan context`: the repro evidence, then the accepted plan with
  its change, Scope, Not in scope, Files, any known limit or deferral,
  and Test plan.
- `## Candidate PR`: `### Title`, `### Description`, `### Commits`,
  `### Diff`, and `### Test evidence`.

The bundle is the whole world.

**Live mode** (your own PR, from the top folder of your fork clone,
on your branch):
- the plan is `plan.md`, including `## Deviations`;
- the diff is `git diff main...HEAD` (with `--stat` for the file
  list), and the commits are `git log --oneline main..HEAD`;
- the title is line 1 of `pr_draft.md`, and the description is the
  rest of that file;
- the captured output is `test_evidence.md`;
- the issue thread is `gh issue view <n> -R <repo> --comments`;
- the repo's asks are `.github/PULL_REQUEST_TEMPLATE.md`,
  `docs/CONTRIBUTING.md`, and the house rules in `scope.md`.

## Plan fidelity (harness category: silent-drift)

- **Where it lives (eval):** the plan's change sentence, its
  `Scope:` and `Not in scope:` lines, its `Files:` list, and any
  known-limit or deferral sentence, all in `## Plan context`. Read
  these against every hunk header (`--- a/<file>`, `@@`) in
  `### Diff`, against `### Commits` (a commit titled "refactor ...",
  "add ... option", or "bump ..." is a lead), and against the fidelity
  claims in `### Description`.
- **Where it lives (live):** the Scope, Files, and Approach sections
  of `plan.md`, plus its `## Deviations`. Read them against
  `git diff main...HEAD --stat` and each hunk of the full diff, and
  against the Summary and Changes bullets in `pr_draft.md`.
- **What good looks like:**
  - Every changed file is in the plan's Files list, is a regression
    test for the issue's case, is an entry the repo's asks require
    (changelog, whatsnew, i18n), or is named in a deviation note.
  - Every deliverable the plan names shows up in some hunk.
  - Each Changes bullet in the description maps to a hunk, and each
    hunk maps to a bullet.
  - A deferral is honest when it is in the plan (a not-in-scope line,
    a known limit, or a `## Deviations` entry) and the description
    restates it ("deliberately out of scope ...", "not handled here
    ...").
- **Silent drift looks like this, in each direction:**
  - *More than the plan:* a second or third commit that refactors a
    helper, renames members, or adds a setting or flag the plan never
    named or scoped out, or a hunk in a file the plan never mentions.
    The description then says "exactly as planned" or "no functional
    changes outside X".
  - *Less than the plan:* a named deliverable (a docs page, a
    changelog entry, a second fix site) is absent from the diff while
    the description says it was done.

## Test evidence (harness category: not-tested)

- **Where it lives (eval):** `### Test evidence`, plus any
  before/after blocks in `### Description`. Read them against the
  plan's `Test plan:` sentence (each case it names, with its expected
  after) and against the repro evidence's trigger at the top of
  `## Plan context`. The repo's required checks are in the repo-facts
  lines (for example "pass cargo test", "yarn test", "pre-commit",
  "fmt, clippy").
- **Where it lives (live):** the Testing section of `pr_draft.md`,
  backed by `test_evidence.md`. Read them against the Test plan
  section of `plan.md` and the repro steps in your posted repro
  comment. The repo's checks are the four the PR template lists:
  `make test-unit`, `make test-integration`, `make lint`, and
  `make typecheck`.
- **What good looks like:**
  - The primary repro's trigger is written out: the exact command, or
    the UI steps as a key sequence.
  - The before is shown or quoted (from `main` or the repro
    evidence), and the after is the observed result on the branch:
    the output, the exit code, the value, or what is shown on screen.
  - The after matches the plan's expected-after.
  - Every other case the test plan names (a second failure mode, a
    control) is reported, even in a sentence.
  - Each named check has a stated outcome: the command and its
    result, such as `make lint` with "All checks passed!" or
    `pytest ... 377 passed, 52 xfailed`. A check that fails or can't
    run is still evidence when it is pasted as-is with the reason
    (Path Review's `make test-integration` reports "no tests ran").
- **Not decisive looks like:**
  - "tested locally, works now", "verified working on my machine",
    or "all tests pass" with no command named;
  - a run of the control or a single-file case, or a different
    request than the one that broke;
  - a named second failure mode that never appears;
  - a check the repo requires that is never mentioned.

## Diff quality (harness category: unreviewable)

- **Where it lives (eval):** every `+` and `-` line of `### Diff`, and
  the messages in `### Commits`.
- **Where it lives (live):** `git diff main...HEAD`, read line by
  line, and `git log --oneline main..HEAD`.
- **What good looks like:**
  - Each hunk changes behavior the plan names, or adds a test.
  - Added comments explain the fix.
  - Unchanged code is not re-printed.
  - Commit messages say what each commit does.
- **Debris tells:**
  - `+` lines that are commented-out code: `// eprintln!(...)`,
    `# print(...)`, or a commented-out "first attempt";
  - live debug output: `eprintln!("DEBUG ...")`, or a `print` used
    for tracing;
  - dead functions: `fn _unused_...`, `#[allow(dead_code)]`, or a
    helper nothing calls;
  - a `TODO` or `FIXME` added on a line the fix doesn't need;
  - a block where the `-` and `+` lines do the same thing in a
    different layout (regrouped `use` or import lines, a struct
    literal joined onto one line, re-indented or identical lines
    printed again);
  - commit messages like "wip", "fix", or "misc cleanups while
    debugging".
- In live mode, any of `plan.md`, `comment.md`, `pr_draft.md`, or
  `test_evidence.md` in the `--stat` list is debris. So is a `print`
  left in `rag/` or a test.

## Standards and comms (harness category: standards-wall)

- **Where it lives (eval):** the repo-facts `pull requests:` line.
  List each ask in it: named sections, checklist items, "closes #xxxx"
  or "fixes #<n>" links, changelog, whatsnew, or release-notes
  entries, title conventions. Then the `contribution policy` line,
  for AI rules. Read these against `### Title`, `### Description`,
  `### Commits`, and the diff's file list (an entry must be a file in
  the diff, not just a claim).
- **Where it lives (live):** `.github/PULL_REQUEST_TEMPLATE.md`
  (Summary, Issue with `Closes #`, Changes, Testing checkboxes,
  Screenshots / Demo, Notes for Reviewers), `docs/CONTRIBUTING.md`
  (the branch name `<type>/<issue-number>-<slug>`, Conventional
  Commits, "fill out the PR template completely", and removing the
  xfail marker for a seeded bug), and the house rules in `scope.md`.
  Read them against `pr_draft.md`, `git branch --show-current`, and
  the commit list. The course's AI-use disclosure goes in Notes for
  Reviewers.
- **What compliant looks like:**
  - Every template section the repo names has content specific to
    this PR, or "N/A" with a reason.
  - The required issue link names this issue's number.
  - The checklist items are answered, and only true boxes are ticked.
  - A required changelog or whatsnew entry is a file in the diff.
  - Where the policy requires disclosure, the description names the
    AI tool and what it was used for, such as "I used Claude Code to
    help read the code and draft this; I ran every command myself".
  - Where the policy asks for the author's own words, a first-person
    statement satisfies it.
- **A visibly ignored ask looks like this:**
  - the template's checklist is missing entirely;
  - there is no `closes #` when the template requires it;
  - a whatsnew entry the template requires is absent from the diff;
  - a section is left as the template's placeholder comment;
  - there is no AI disclosure under a policy that says all AI usage
    must be disclosed.
- (Whether the description's claims match the diff belongs to plan
  fidelity, above.)
