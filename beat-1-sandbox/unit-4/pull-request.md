# Unit 4 — Test and Submit

Path: `beat-1-sandbox/unit-4/pull-request.md`

---

## Your pull request

**Pull request**

https://github.com/codepath/pathreview-ai301-fa26-s3/pull/238

**Branch**

fix/69-json-array-fallback

## Eval iterations

**Run history**

1. Full run 1: **agreement 20/20 scored items (bar: 18/20: PASS)**.
   - Categories: clear-accept 7/7, not-tested 4/4, silent-drift 4/4,
     standards-wall 2/2, unreviewable 3/3.
   - This is the run in `eval-run.txt`.
   - I set `PYTHONUTF8=1` before this run. In Unit 3, Windows' cp1252
     stdin stopped a package's prompt from reaching `claude`.
   - No row disagreed, so I did no `--only` re-runs and no second full
     run. The rubric, procedure, evidence guide, and SKILL.md hashes in
     the `eval-run.txt` header are the files now in
     `tools/pr-precheck/`.

**Package analysis**

pkg-18 (nushell/nushell#18850, unreviewable). My rubric decided
`reject`, and the gold label is `reject`.

This is one of the arguable packages. The fix itself is right:
`let colorize = std::io::stdout().is_terminal();` gates the colors,
which is the plan's one bounded change. The evidence is decisive:
`grep -c` counts 214 escape sequences before and 0 after, and the tty
control is reported.

So seven of the eight required checks passed, and only `diff-clean`
failed: "Adds commented-out '// eprintln!("help colors: tty=...")'
plus regrouped use imports and identical println lines removed and
re-added". `diff-within-plan` passed because every hunk is in
`src/command.rs` or the test, and both are on the plan's Files list.
The grader followed procedure step 2 of Check execution: "Do not fail
a check for a problem that another check owns". The churn belongs to
`diff-clean`.

The deciding fact is the commented-out `eprintln!`. The import regroup
alone is debatable, because the fix does need one new `use` line. But
`diff-clean` fails "commented-out code (a commented-out debug
print...)" with no judgment call. So even a grader who reads the
import hunk as needed still holds the package.

**Check rationale**

| `diff-clean` | The added and removed lines of every hunk, and the commit list. | Pass if nothing a reviewer must skip rides along with the change. Fail if the diff adds any of these: commented-out code (a commented-out debug print, or a "first attempt" kept "for reference"); a debug print or trace left in (`eprintln!("DEBUG ...")`, a stray `print(...)`); a dead function or variable (unused, named `_unused_...`, or kept alive with `allow(dead_code)`); a TODO or FIXME on a line the fix does not need; or a block whose removed and added lines do the same thing and differ only in formatting or order (regrouped imports, a re-wrapped struct literal, re-indented or identical lines printed again). Comments that explain the fix are not debris. A block that the diff re-lists next to a real change in it (the same call re-printed with one argument changed) is not churn. Live: `plan.md`, `comment.md`, `pr_draft.md`, or `test_evidence.md` in the diff is debris. | required |

I wrote the fail list from the debris in the three unreviewable
packages, so each item is a thing a grader can point at, not an
adjective like "messy":
- pkg-12: a commented-out `DBG` line, a commented-out first attempt,
  `_unused_buffer_probe`, a TODO on an import, and a re-printed `while`
  loop.
- pkg-15: an `eprintln!("DEBUG accept err ...")`, commented-out `lsof`
  lines, `#[allow(dead_code)] count_open_fds`, and a struct literal
  joined onto one line.
- pkg-18: a commented-out `eprintln!`, regrouped `use` lines, and
  identical `println!` blocks.

The two "not debris" sentences came from reading the clear accepts
before the first run:
- pkg-13, pkg-16, and pkg-19 all add or replace a comment next to the
  fix. A rule like "added comments are debris" would fail them.
- pkg-02's `save_html` and `save_svg` hunks remove and re-add the
  unchanged call lines (`html = self.export_html(`, `theme=theme,`)
  around the one real change, `clear=clear` to `clear=False`. pkg-16 re-prints its
  `if err := machine.DoLoadImages(...)` line the same way. A literal
  reading of "identical lines printed again" would fail both gold
  accepts.

I rejected a general "no formatting churn" rule for the same reason:
it can't tell pkg-02's diff layout from pkg-18's re-printed blocks.

The live sentence is there because the assignment says never to
`git add` those four files. If one gets committed by mistake, this
check is what catches it before the PR goes out.

**Trade-offs**

The re-listed-block exemption gives up some strictness. If a hunk
changes one argument and also re-indents the lines around it, it
passes `diff-clean`: the reformatting hides inside a block that
carries a real change. I accepted that miss so that pkg-02 and pkg-16
stay accepts. Pure churn blocks, like pkg-12's `while` loop and
pkg-18's `println!` lines, are still caught, because they carry no
real change.

A second cost showed up in run 1. `repro-rerun` asks for "the before
shown or quoted". pkg-09's evidence shows the trigger and the after,
but not the before, so that check failed there. pkg-09 is a gold
reject on drift anyway, so the verdict didn't change. But a ready PR
that only shows the after would be held by this check. I kept the
rule because a before/after pair is what makes evidence decisive in
pkg-04 and pkg-10.

How I know nothing else changed: there was one full run and no file
edits after it. The `rubric.md` hash `f82257d864ecda65` in
`eval-run.txt` matches the file in `tools/pr-precheck/`.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/pr-precheck/`.
