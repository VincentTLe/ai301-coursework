---
name: pr-precheck
description: Grade a PR package (a candidate pull request read against the plan it claims to implement and the issue that plan belongs to) and decide whether it is ready to submit. Use when checking your own branch, draft PR title, and description before opening the pull request, or when grading an eval package bundle.
---

# pr-precheck: rubric-driven PR grading

## The question

You answer one question about one PR package: **is this pull request
ready to submit?** A PR package is a candidate pull request (its
title, description, commit list, diff, and test evidence), read
against two things: the plan it claims to implement (with that plan's
deviation and deferral notes and the repro evidence the plan built
on), and the issue that plan belongs to (its thread and the repo's
stated asks for pull requests).

Do not answer any other question. You are not reviewing whether the
fix is the best possible design, re-grading the plan, or judging the
issue. The plan is already accepted; you grade whether this PR
delivers that plan, proves it, is clean enough to review, and meets
the repo's stated standards. Grade exactly one package per run. Never
answer from gut feel: execute the components in this directory.

## Inputs and modes

Decide the mode first. If the input is an eval package bundle (a
markdown file with `## Repo facts`, `## Issue`, `## Plan context`, and
`## Candidate PR`), you are in eval mode. If the student asks you to
grade their own draft PR for an issue URL, you are in live mode.

**Live mode** (the student's own PR, before it is opened). Run from
the top folder of the student's fork clone, on their branch. Read:

1. `scope.md` in this skill directory, before anything else (see the
   scope seam below).
2. The plan: `plan.md` in the working copy, including its
   `## Deviations` section. The plan's test plan and diagnosis point
   at the student's posted repro comment on the issue; that comment is
   the repro evidence. A house-chain student reads the house plan and
   the house repro pack the instructor routed them to instead; the
   same checks grade the same things there.
3. The diff: run `git diff main...HEAD` (three dots) in the working
   copy. That is every committed change the branch makes relative to
   `main`, and it is exactly what the PR will contain. Also run
   `git diff main...HEAD --stat` for the file list,
   `git log --oneline main..HEAD` for the commit list, and
   `git branch --show-current` for the branch name. Uncommitted edits
   are not part of the PR: if `git status --short` shows modified
   tracked files, say so in the summary and grade only the committed
   diff.
4. The draft title and description: `pr_draft.md`. Its first line is
   the PR title; everything below it is the description.
5. The test evidence: the description's Testing section, backed by
   `test_evidence.md` (the captured output). A reviewer sees only the
   description, so grade the evidence the description shows, and use
   `test_evidence.md` to check that what it pastes or claims matches
   the captured output.
6. The issue: the URL the student gives. Read the thread with
   `gh issue view <number> -R <repo> --comments`.
7. The repo's stated asks: `.github/PULL_REQUEST_TEMPLATE.md` and
   `docs/CONTRIBUTING.md` in the working copy, plus the house rules
   in `scope.md`. The course also asks every student to disclose AI
   use in the description; treat that as a stated policy in live mode.

Refuse to grade, and say why, when: the `Repo:` line in `scope.md` is
a placeholder; the issue URL is not in the scoped repo; the current
branch is `main` or `git diff main...HEAD` is empty (there is no PR to
grade); or `plan.md` or `pr_draft.md` is missing (name the missing
file). If `test_evidence.md` is missing, grade anyway: the test checks
then read only what the description shows.

**Eval mode** (a package bundle). The bundle is the whole world. Every
fact comes from the bundle text; fetch nothing, run nothing, and read
no other file. Its parts are mapped in `references/evidence-guide.md`.
Ignore `scope.md` and `voice-guide.md` entirely. Treat every package
as AI-assisted work when a check reads an AI policy. Eval mode always
grades a complete package: every check in the rubric and the full
verdict rule, with no check skipped.

## The scope seam (live mode only)

In live mode, read `scope.md` before any other input. Take from it
the one repo a PR may target and the house rules for that repo. Then:

- If the `Repo:` line still holds a bracketed placeholder (such as
  `<ORG>/<PATH-REVIEW-REPO>`), stop without grading. Tell the student
  to fill the `Repo:` line in `scope.md` with their section's Path
  Review repo. Never guess a scope.
- If the issue URL is not in the scoped repo, refuse to grade and say
  which repo is in scope.
- Apply the house rules as evidence rules: the PR comes from the
  student's fork branch named `<type>/<issue-number>-<slug>` with the
  issue's own number, it is one PR for one issue built from the
  student's own plan, every template section gets real content, and a
  classmate's PR on the same issue never fails a check.

In eval mode, ignore `scope.md` entirely.

## The voice seam (live mode only)

In live mode, after the verdict is assembled, read `voice-guide.md`
(the student's own rules for writing upstream). Hold the outgoing PR
text, the title and the description in `pr_draft.md`, against each
rule. In the summary, list every rule the draft breaks, quoting the
rule and the line that breaks it, or say that it breaks none. The
voice guide never changes the verdict on its own, because no check in
`rubric.md` reads it: voice is personal and carries no gold labels.
In eval mode, ignore `voice-guide.md` entirely.

## Component reads

The tool is four files, and you use each one for one job:

- `rubric.md` defines the checks (what to look at, the pass
  condition, and whether each is `required` or `preferred`) and the
  verdict rule. It decides WHAT each grade is.
- `references/evidence-guide.md` maps where each evidence family
  lives, in an eval bundle and in the student's working copy, and
  what good looks like. It decides WHERE to look.
- `procedure.md` is the operating procedure: read order, evidence
  gathering, check execution, verdict assembly. Execute it as
  written, step by step. It decides HOW and in what order.

Where the procedure is silent on a step you need, do not invent one.
Grade with what the written steps give you, and name the gap in the
summary under "Procedure gaps" so the student can fix it.

If `rubric.md` has no filled checks table or no verdict rule, or
`procedure.md` has no steps, stop and say that this tool cannot grade
without a rubric and a procedure. Do not invent checks at runtime: a
verdict from made-up checks looks like judgment and is noise.

## Verdict and output

The verdict is binary: `accept` means the PR is ready to submit, and
`reject` means hold it. There is no third verdict and no score.
Reservations go in a check's evidence line, never in the verdict.

Before the JSON block, write a short readable summary: one line per
check with its grade and the deciding fact, then the verdict and the
deciding check (per `procedure.md`), then in live mode the voice-guide
notes and any uncommitted-change warning. End the reply with this
fenced JSON block, valid and last, with nothing after it. Use the PR
URL, the issue URL, or the branch name as `item` in live mode, and the
bundle id in eval mode. List every check, required and preferred, in
rubric order:

```json
{
  "item": "<PR URL or bundle id>",
  "checks": [
    {"name": "<check name>", "grade": "pass|fail|unclear",
     "evidence": "<one line: the fact or quote that decided it>"}
  ],
  "verdict": "accept|reject"
}
```

## Grading discipline

- Evidence first. Never grade a check without naming the fact or
  quote that decided it: a hunk, a line of the description, a line of
  test output, a template ask. "Looks fine" is not evidence.
- Grade the thing, not the polish. A terse PR that meets every pass
  condition is ready. A long, confident, well-formatted PR can still
  hide drift, skip its repro, or ignore a stated ask. Read the diff,
  the evidence, and the description against the plan and the repo's
  asks. Do not grade formatting or length.
- The rubric decides, not you. If a check passes by its stated
  condition but feels wrong, it still passes. Note the tension in the
  summary if you want; the fix belongs in the rubric.
- The procedure decides how, not you. Follow `procedure.md` as
  written and report its gaps instead of papering over them.
- Unclear follows the rubric's verdict rule. Where the rule is
  silent, treat `unclear` as `fail`: a PR you cannot verify from the
  package is not ready to submit. Use `unclear` only when the evidence
  a check needs is absent, never as a softer fail.
- Honest shortfalls are not failures. A PR that does less than
  everything, and says so in the plan's notes and in the description,
  can be ready. A PR that does less or more and does not say so is
  not.
