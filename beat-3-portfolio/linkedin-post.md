<!-- SCAFFOLD. Delete everything in this comment once you've written the post below it.

Rules: 60-250 words (a link = 1 word, a hashtag = 1 word). Plain text: no headings, tables, or code blocks.
First person, complete sentences. Don't blame the project, its maintainers, or the course.
Say "opened a fix", NOT "fixed" (the PR is still open).

Opening 2 sentences: start from the two LinkedIn lines you wrote in lecture. They must say what you did.

Facts you can use (all backed up in technical-writeup.md), in plain words:
- pyfixest: an open-source Python library that economists and researchers use to run regressions.
- The problem: one of its statistical checks (randomization inference, which asks "could this result be luck?")
  refused to run on weighted data. Weighted data = some rows count more than others, which surveys use a lot.
- What I found: simply removing that block was not enough, because a faster shortcut inside the check quietly
  ignored the weights and could give a wrong answer.
- What I did: wrote the fix and tests that prove the fast and slow methods now agree, then opened a pull request
  on 2026-11-04.
- A lesson: the reference tool I planned to compare against (the R version) turned out to ignore weights too,
  so I dropped it rather than lock in a wrong answer.
- How it ended: the pull request is open and waiting for the maintainers' review. I kept it up to date when a
  new release changed the code under it.
- Why it matters / what it taught you: check an answer two independent ways; follow a project's own rules
  (draft pull request for AI-assisted work); [your own takeaway].

End with exactly one link: https://github.com/py-econometrics/pyfixest/pull/2024
Hashtags (optional) go after the link, e.g. #OpenSource #Python #DataScience
-->

[Your opening two sentences, from your lecture lines]

[The rest of the post in your own words]

https://github.com/py-econometrics/pyfixest/pull/2024
