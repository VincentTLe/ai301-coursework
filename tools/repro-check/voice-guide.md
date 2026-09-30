# Voice guide: how I talk upstream

## Who I am in threads

I'm a student in AI301 making my first open-source contributions,
working in Path Review alongside classmates. I use Claude Code to help
me read code and draft, but I run every command myself and only post
what I have seen on my own machine. Readers can expect a specific
report of what I did and saw, and a clear line between what I know and
what I suspect.

## Rules I write by

### Rule: promise the investigation, not the fix

Before I have reproduced anything, my comment says what I will look at
and that I will report back. It never promises a fix, a PR, or a date.

- Wrong: "I'll have a fix for the array fallback up by the weekend."
- Right: "I'll run `test_json_array_fallback` without the xfail marker and post the environment, commands, and output here."

### Rule: never say "reproduced" before I have the output

The words "reproduced" or "confirmed" only appear next to the output
that shows it. Before that, I say what I expect to find, and label it
as what the issue says.

- Wrong: "I reproduced the AttributeError and it's definitely in `_parse_json_output`."
- Right: "The issue reports `AttributeError: 'list' object has no attribute 'items'` from `output_parser.py`; I'll check whether I see the same on a fresh clone."

### Rule: name this issue's specifics

Every comment names at least one thing that only makes sense on this
issue: the error, the file, the function, or the test.

- Wrong: "Hi, I'd like to work on this issue, please assign it to me."
- Right: "I'd like to investigate the top-level JSON array case in `rag/generator/output_parser.py` (#69)."

### Rule: a guess is labeled as a guess

When I think I know the cause but haven't shown it, I say "I suspect"
or "my hypothesis is" and name what would confirm it.

- Wrong: "The root cause is that the fallback assumes a dict."
- Right: "My hypothesis is that the fallback path assumes a dict; the traceback line will show whether `.items()` is where it fails."

### Rule: say how AI helped

I say in one plain line that I used an AI assistant and for what,
even when the repo doesn't require it.

- Wrong: (no mention, when Claude drafted half the comment)
- Right: "I used Claude Code to help draft this comment; I ran every command myself."

## Things I never post

- "Please assign this to me" or "please reserve this for me".
- Any fix date or "guaranteed".
- "Same as above", "+1", or "can confirm" in place of my own run.
- A root cause stated as fact without the output that shows it.
- Compliments to the project standing in for content.
- A comment I haven't run through repro-check in live mode.
