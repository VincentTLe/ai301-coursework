# Procedure: how this skill grades a plan package

## Read order

1. Live mode only: read `scope.md`. Confirm the issue URL is in the
   scoped repo; if it is not, or the `Repo:` line is a placeholder,
   stop as SKILL.md says. Note the house rules.
2. Read `rubric.md` and `references/evidence-guide.md`. Write down the
   list of check names, which are `required`, and the verdict rule.
3. Read the repo facts. Write down the AI policy kind (1, 2, or 3 as
   the evidence guide's Comms section defines) and the template asks.
4. Read the issue (title and body). Write down the reported behavior
   in one line: the symptom and its trigger.
5. Read the repro evidence next, BEFORE the thread and the plan, so
   the facts are fixed before any confident narrative is read. For
   each step and control, write one line: what was changed and what
   came out. Then write where the evidence shows the fault first
   appearing (which step, which component) and what each control
   rules out.
6. Read the thread highlights. Write down each maintainer-direction
   item and each prior-art PR, with the author's role. If the thread
   offers a cause, note whether the step-5 notes support it or
   contradict it.
7. Read the candidate plan, then the candidate plan comment. Do not
   grade yet.

The order matters because `diagnosis-grounded` and `fix-at-cause` are
graded against the step-5 notes, not against the plan's own account
of the evidence, and `thread-engaged` needs the step-6 list complete
before the comment is read.

## Evidence gathering

For each check, pull the evidence below and quote it (eval mode) or
cite where it came from (live mode).

1. `diagnosis-grounded`: quote the plan's cause sentence. Next to it,
   list every control from the step-5 notes.
2. `fix-at-cause`: quote the plan's change (the Change or Approach
   lines). Next to it, put the step-5 note about where the fault first
   appears.
3. `scope-bounded`: list every distinct change the plan proposes, one
   per line, from its Proposed changes, Approach, Scope, and Files
   sections. Mark each as fix, sibling fix, test, deferred, or extra.
4. `executable`: quote the Files line (or "none") and the approach
   steps. Mark each step as "a change" or "an investigation", and note
   any "or", "whichever", "somewhere", or "not sure" that leaves a
   decision open.
5. `test-decisive`: quote the test plan. Note the observable outcome
   it names, if any, and whether the broken code would fail it.
6. `thread-engaged`: take the step-6 list. For each item, quote the
   part of the plan comment that engages it, or write "not mentioned".
7. `ai-disclosure`: take the policy kind from step 3 of Read order.
   Quote the plan comment's AI sentence, or write "none".
8. `unknowns-named` and `comment-matches-plan`: quote the risk or
   unknown lines, and note any promise in the comment that is not in
   the plan.

Live mode: the issue thread, CONTRIBUTING, and AI policy come from
`gh` (see the evidence guide). The repro evidence comes from the
student's posted repro comment on the issue; if the drafts quote repro
output, check that the quote matches the posted comment.

## Check execution

1. Run the required checks in rubric order: `diagnosis-grounded`,
   `fix-at-cause`, `scope-bounded`, `executable`, `test-decisive`,
   `thread-engaged`, `ai-disclosure`. Then run the two preferred
   checks.
2. Grade each check only against its gathered evidence and its own
   pass condition in `rubric.md`. Do not let one check's result change
   another's: a plan with a wrong cause can still be bounded and
   executable, and a polished plan can still fail one check.
3. Apply the pass condition literally. If the condition's wording
   leaves you choosing between pass and fail, quote the deciding text
   and pick the reading the condition's examples point to.
4. Grade `unclear` only when the evidence the check needs is not in
   the package at all (for example, no repro evidence block, or no
   plan comment). If the evidence is present but weak, it is a pass or
   a fail, not `unclear`.
5. Every grade gets a one-line evidence string: the quote or fact that
   decided it. "Looks fine" is not evidence.
6. Do not re-read the whole package for each check. Re-read only the
   part the check's Evidence column names, unless the gathered notes
   are missing something.

## Verdict assembly

1. Collect the seven required grades.
2. If all seven are `pass`, the verdict is `accept`.
3. If any required grade is `fail` or `unclear`, the verdict is
   `reject`. `unclear` counts as `fail`.
4. Preferred grades are reported but never change the verdict.
5. In the summary before the JSON, name the deciding check or checks
   for a reject, with their evidence quote; for an accept, say that
   every required check passed.
6. Live mode only: after the verdict, check the plan comment against
   `voice-guide.md` and list any rule it breaks, quoting the rule.
   This never changes the verdict.
7. Emit the JSON block in SKILL.md's format, with every check (required
   and preferred) in rubric order, and nothing after it.
