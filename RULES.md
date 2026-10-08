# Rules

Five rules for working with an agent that runs long. They are short because
each one exists to prevent one specific failure: the first four, a way a run
lies about itself; the fifth, a way it spends the person's attention. Copy the
ones you want into your agent's instructions (`CLAUDE.md`, `AGENTS.md`).

## 1. The command decides, not the judgement

"Done" is said after a command exited zero or a measurement was read — never
because the work *looks* finished.

Running out of attempts, time, or ideas is the reason you stopped. It is not
evidence that the thing works. A run that hits a limit reports what is still
unverified, and says so in those words.

## 2. Never repeat an identical attempt

If a fix left the tree exactly as it was and the error is the same as before,
stop and change approach. Re-running the same command against the same state
produces the same red and teaches nothing.

This matters most where nobody is watching: inside an autonomous loop, the
counter keeps going and every wasted round looks like progress.

## 3. A check you have never seen fail is not a check

Before trusting a new test, gate, or assertion, make it fail once on purpose.
A green that cannot go red measures nothing.

Two questions catch most useless checks:
- *Has this ever been observed failing?*
- *In what state does it measure?* — a check that runs against the wrong tree,
  the wrong environment, or a stale build is green for the wrong reason.

## 4. A lesson seen once is an anecdote

Memory that is loaded on every session becomes law. Something observed a single
time does not belong there: keep it, but leave it out of the index until it
happens again. Promote on the second occurrence, on explicit request, or when
it is a security fact — those cost more to delay than to get wrong.

The same applies in reverse: a memory that turns out to be stale gets demoted,
not left to keep asserting something false.

## 5. A decision is a menu, not a paragraph

When the agent needs the person to choose, it asks through the harness's choice
tool (`AskUserQuestion` in Claude Code, `request_user_input` in Codex and Muse
when they offer it), not with a numbered list at the bottom of a long reply. One question per decision. The
recommended option goes first, with one line on why. Free text stays open,
because most real answers are not on the list.

A written list is the fallback where no such tool exists or it is switched off,
and only there. In a headless run nobody can answer at all: the agent decides,
and says what it chose.

A menu makes asking cheap, and that is its risk. A choice the agent can make
and undo by itself is not a question: if every answer leads to the same work,
decide and move on. `skills/grill-me` asks this way.

Why menus that ask, and not menus that guess what you will type next:
[docs/menus-that-ask.md](docs/menus-that-ask.md).

## Why these five

The first four came out of runs that reported success without earning it: a
loop that spent two hours re-running an unchanged failing check, a critic that
approved work it never inspected, an audit that searched with a tool which
silently skipped the files that mattered. In each case the machinery was fine
and the *ending* was wrong — the run had no way to distinguish "verified" from
"out of road".

The fifth came from somewhere else, and nothing lied. A month of one person's
replies to their agents showed that most bare "go" answers (58 of 79) came
right after the agent had written a decision out as a list. It protects the
person's attention, not the run's honesty.
