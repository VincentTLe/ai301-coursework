# Procedure: how this tool grades a PR package

## Read order

1. **Live mode only:** read `scope.md` first.
   - Confirm that the `Repo:` line is filled and that the issue URL is
     in that repo. If not, stop as SKILL.md says.
   - Confirm that the branch is not `main` and that
     `git diff main...HEAD` is not empty. If either fails, stop as
     SKILL.md says.
   - Note the house rules.
2. Read `rubric.md` and `references/evidence-guide.md`. Write down:
   - the check names in rubric order;
   - which checks are `required`;
   - the verdict rule.
3. Read the repo's stated asks: the repo-facts `pull requests:` and
   `contribution policy` lines, or live the PR template,
   `docs/CONTRIBUTING.md`, and the house rules. Write down:
   - each ask as a numbered list (A1, A2, ...): every named section,
     checklist item, required link, required entry, and title or
     commit convention;
   - the AI-policy kind: disclosure required, own words required, or
     none.
4. Read the issue. Write down the reported behavior in one line: the
   trigger and the symptom.
5. Read the plan, BEFORE any part of the candidate PR. Write down:
   - the deliverables as a numbered list (D1, D2, ...): each fix
     site, named test, and named docs, changelog, or i18n item;
   - the Files list;
   - the not-in-scope items;
   - every recorded note: a known limit, a deferral, or, live,
     `## Deviations`;
   - the test-plan cases (T1 = the primary repro with its
     expected-after, then T2, ... for each other failure mode or
     control it names);
   - the checks the test plan names.
6. Read the diff and the commit list next, BEFORE the title,
   description, and test evidence. Write down:
   - each hunk as a numbered list (H1, H2, ...), with its file and
     what its lines actually do;
   - each commit message.
7. Read the title and description. Write down each claim about what
   the PR contains as a numbered list (C1, C2, ...): fidelity claims,
   files, entries, docs, tests, and cases covered.
8. Read the test evidence last (eval: `### Test evidence`; live: the
   description's Testing section, then `test_evidence.md`). Write
   down each run it shows: the trigger, the before, and the after.

**Why this order matters.** Each step fixes facts before the next part
can tell a story about them:

- The plan is fixed (step 5) before the diff, so you read the diff
  against the plan, not against the description's account of it.
- The hunks are fixed (step 6) before the description. A confident
  "exactly as planned" can't hide a hunk you have already listed, and
  the claims are checked against hunks you already know.
- The evidence comes last (step 8), so it is read against the
  test-plan cases from step 5.

## Evidence gathering

Each check's evidence is a pairing of two lists from the read order.
Build each pairing below and keep it for check execution. In eval
mode, quote the bundle. In live mode, cite the file, the command, or
the hunk.

1. **`diff-within-plan`.** For each hunk H, write which deliverable D
   it serves, or one of: test for the issue's case, required entry
   (name the ask A), deviation note, or **extra**. For every hunk
   marked extra, also note whether it touches a file outside the
   Files list or an item on the not-in-scope list.
2. **`plan-delivered`.** For each deliverable D, write the hunk(s)
   that deliver it, or **missing**. For each missing D, quote the
   plan note that defers it and the description sentence that
   restates it, or write "none".
3. **`description-matches-diff`.** For each claim C, write the hunk
   that makes it true, or **contradicted**, naming the hunk or the
   missing file. Mark loose wording about size or emphasis as
   "loose, not a contradiction".
4. **`repro-rerun`.** For each test-plan case T, quote the run from
   step 8 that re-runs it, or write **not mentioned**. For T1, also
   mark three things: whether the trigger is written out, whether
   the before is shown or quoted, and whether the after matches the
   expected-after. Also mark whether the run is the same case as T1
   or a different one (a control, a single-file case, a different
   request).
5. **`repo-checks-reported`.** List every check the test plan names
   and every check an ask A requires. For each one, quote its
   reported outcome with the command named, or write
   **not reported**.
6. **`diff-clean`.** Scan every `+` line, and mark each one that is:
   - commented-out code;
   - a debug print or trace;
   - a dead function or variable, or `allow(dead_code)`;
   - a TODO or FIXME.

   Then compare the `-` and `+` lines of each hunk. Mark any block
   whose lines do the same thing in a new layout or order and carry
   no change the fix needs. Live mode: also check `--stat` for
   `plan.md`, `comment.md`, `pr_draft.md`, and `test_evidence.md`.
7. **`template-honored`.** For each ask A, quote where it is met (the
   description line, the checklist answer, or the diff file for an
   entry), or write **ignored**. Live mode: also record the branch
   name against `<type>/<issue-number>-<slug>`, and each ticked box
   against the evidence that backs it.
8. **`ai-disclosure`.** Take the AI-policy kind from step 3. Quote
   the description's AI sentence, or write "none".
9. **The preferred checks.** For `commits-describe-change`, take the
   commit messages from step 6. For `regression-test-targets-case`,
   note which case each added test exercises.

Live sources:
- The issue thread comes from `gh`.
- The diff comes from `git diff main...HEAD`. Never use the working
  tree for it, and never use a single commit.
- When the description pastes output, check it against
  `test_evidence.md`. A pasted result that the capture does not
  contain counts as not shown.

## Check execution

1. Run the required checks in rubric order:
   1. `diff-within-plan`
   2. `plan-delivered`
   3. `description-matches-diff`
   4. `repro-rerun`
   5. `repo-checks-reported`
   6. `diff-clean`
   7. `template-honored`
   8. `ai-disclosure`

   Then run the two preferred checks.
2. Grade each check against its own pairing from evidence gathering
   and its own pass condition in `rubric.md`, and nothing else.
   - Do not let one check's grade change another's. A correct fix can
     still fail `diff-clean`, and a messy diff can still pass
     `repro-rerun`.
   - Do not fail a check for a problem that another check owns. For
     example, an extra hunk the description never mentions is a
     `diff-within-plan` fail, not a `description-matches-diff` fail.
3. Apply the pass condition literally. If its wording leaves a choice
   between pass and fail, quote the deciding text and pick the
   reading that the condition's own examples point to.
4. Grade `unclear` only when the evidence a check needs is absent
   from the package (for example, no `### Test evidence` section and
   no before/after in the description). Evidence that is present but
   weak is a pass or a fail, not `unclear`.
5. Give every grade a one-line evidence string: the hunk, quote, or
   missing item that decided it.
6. Do not re-read the whole package for each check. Re-read only the
   part the check's Evidence column names, and only when the pairing
   from evidence gathering is incomplete.

## Verdict assembly

1. Collect the eight required grades.
2. If all eight are `pass`, the verdict is `accept`.
3. If any required grade is `fail` or `unclear`, the verdict is
   `reject`. `unclear` counts as `fail`.
4. The preferred grades are reported but never change the verdict.
5. The deciding check is the first failing (or `unclear`) required
   check in rubric order. In the summary before the JSON, name it and
   quote its evidence line, then list any other failing required
   checks. For an accept, say that every required check passed.
6. **Live mode only,** after the verdict:
   - hold the title and description against `voice-guide.md`, and
     list each rule they break, quoting the rule (this never changes
     the verdict);
   - report any uncommitted changes to tracked files;
   - report any procedure gaps.
7. Emit the JSON block in SKILL.md's format, with every check
   (required and preferred) in rubric order, and nothing after it.
