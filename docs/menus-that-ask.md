# Menus that guess vs. menus that ask

I wanted every prompt box to offer me options before I typed anything: a quick
menu, a way to drill down to the exact option, and to correct it visually
instead of retyping. Someone has probably built it already, and I have prompted
my agents a lot, so there should be plenty to learn from.

Before building anything, I measured it on 30 days of my own inputs:

- **Menus that guess** what I will type would have been right on about 6% of
  my inputs at best, almost all of them one-word replies like "go" and "ok".
- **Menus that ask** for a decision the agent already needs are worth having.
  That became [rule 5](../RULES.md#5-a-decision-is-a-menu-not-a-paragraph).
- Two words in a dictation dictionary target 17 of my 21 manual term fixes.

## Measure first

5,620 inputs in 30 days, across five surfaces:

| Surface | Share of inputs |
|---|---|
| OpenClaw, on WhatsApp | 26.6% |
| Claude Code, in the terminal | 24.4% |
| jcode | 18.0% |
| Topics, a desktop app for agent sessions | 17.2% (39.7% of all words) |
| Claude Desktop | 12.3% |

Topics gets a sixth of the messages and two fifths of the words: that is where
the long ones go, and the history menu below never covered one over 40 words.

## Menus that guess

**From my own history.** For each input, a 10-item menu drawn from my past
replies, built without seeing what I typed next. What I actually typed was on
it 5.9% of the time, nearly always a reply of five words or fewer ("go", "ok",
"you there?"). It covered 19.5% of those short replies, 0.6% of replies between
6 and 40 words, and none above 40.

**Written by a model, in my style.** Claude Sonnet, at low effort, got each
context plus 12 of my past replies to similar moments, and wrote 4 options in
my voice. A blind judge compared them with what I really wrote: exact or close
in 4.2% of cases (5 of 120), against 3.3% for the same options matched to the
wrong context. Barely above chance. On the 103 cases that were not a generic
acknowledgement, it hit once. Another 29.2% were partial, right topic and wrong
content: the model knows what I am talking about, not what I am about to say.

**The one built into Claude Code.** Claude Code can suggest your next prompt
in the terminal, and it is on in my setup. I accepted it 2 times in 956
terminal inputs.

**Verdict.** Menus that guess cover the replies that were already cheap to
type and miss the rest. My reading: what I type is mostly information the model
does not have yet, so there is nothing to guess from.

## Menus that ask

The menu that does pay is the one the agent already needs. Agents often end a
reply with choices for me, but almost never through a real menu:
`AskUserQuestion`, Claude Code's built-in choice tool, was called 35 times in
2,495 sessions. My own instructions asked for text instead: a "To decide" block
at the end of the reply, one numbered line per choice, the recommended one
first, answered with "ok" or "ok, but not 2".

In the same 30 days, 79 of my replies were a bare go-ahead, and 58 of them
answered a written list: 38 a "To decide" block, 20 a list of options with no
heading. That is reading a list to type back one word.

So the rule that shipped is about asking, not guessing: where the harness has a
choice tool, decisions go through it, and the written list stays only where
there is none. Roughly:

```text
Before:  To decide
         1. Keep the old endpoint a week (recommended: clients update slowly)
         2. Remove it now
         ok / ok, but not 2

After:   Remove the old endpoint?
         > Keep it a week (Recommended)    clients update slowly
           Remove it now
           Other: type your own answer
```

A menu does not capture most answers either, and should not try. In 2,982
replies that came right after the agent had listed options, I picked one as-is
154 times (5.2%); 52.9% were free text, 23% corrections, 18% questions. So free
text stays, and the menu earns its place on the decision itself: the
recommendation costs one keypress, anything else one typed answer.

## The two-word fix

I dictate many of my messages with Wispr Flow. Of 1,310 dictations, I had
edited 209 by hand before sending. 42.1% of those edits only touched a capital
letter or a final period. 9.1% fixed a term, and 17 of the 21 term fixes were
the same two words: "Claude", heard as "cloud", and "Topics", heard as "Topix".
Another 53 mishearings went to the agents uncorrected.

Both words are now in Wispr Flow's dictionary. Capitals and periods are a style
setting ("Very casual" drops both), but Wispr's docs say styles only apply to
English, and I dictate in Italian, so that part is untested.

## Can it learn how I think?

The next question was whether an agent could learn how I reason from the times
I corrected it. I took 200 of the 436 corrections I made in 90 days and
labelled what each one carried. A second, blind labelling pass agreed on 80%
of cases (Cohen's kappa 0.61).

| What the correction carried | Share |
|---|---|
| New information only I had: a fact, a choice, taste in UI | 64% |
| Mixed: some new information, some reasoning | 21.5% |
| Reasoning the agent could have done by itself | 14.5% |

Of the 72 corrections that reasoning could at least partly have avoided, 30
were already covered by a rule in my instructions. The two most ignored were
"act, don't ask" (9 times) and "done means proof and completeness" (7 times;
its proof half is [rule 1](../RULES.md#1-the-command-decides-not-the-judgement)
here). The other 42 scattered: only three new rules came up three times or
more, 1.5% of corrections each, so none of them shipped. For the record: check
facts on the real system before judging, re-read the brief before delivering,
and simulate real use before proposing, dropping layers with no clear purpose.

The reasoning was already written down; what was missing was compliance.

## The hook I did not build

The obvious next step was a Stop hook that refuses to end a turn on "shall I
proceed?" or "do you want me to...?", so the agent acts instead of asking. I
wrote the pattern against older conversations and scored it offline on the
last 30 days, held out. It would have fired on 4 of 2,315 final messages (0.17
per 100), and none of the 4 was followed by a bare go-ahead, so blocking them
would have saved nothing. A looser version fired 13 times, same result. Of the
79 bare go-aheads it would have caught 0: they came after the lists above (58),
an announcement of what the agent was about to do (13), or a stop on the
system's side (8).

Agents in this setup have mostly stopped ending on "shall I proceed?". What is
left needs judgement, which a regex does not have, and the menu turns the
biggest share into one keypress. Not built.

## Prior art

What I looked at before building anything. I read the code of the first one;
the rest are names, so you can judge for yourself:

- **next-steps** in
  [hamzafer/claude-code-mods](https://github.com/hamzafer/claude-code-mods)
  (MIT): the guessing kind, inside the Claude Code terminal. After each turn it
  shows 2 or 3 likely next prompts above the input; a digit in an empty prompt
  drafts one, 0 dismisses, nothing sends by itself. One Haiku call per finished
  turn, in 178 lines. If you want menus that guess, start there.
- Microsoft's **Promptions** (dynamic prompt middleware, MIT).
- **Superhuman** Instant Reply, **Warp**, **Atuin**, **Cotypist**, **Pretype**.
- **Marking menus**, the radial menus from HCI research.

## Adopt it

Paste this into your `CLAUDE.md`:

```markdown
- When you need me to choose, ask with AskUserQuestion: one question per
  decision, your recommended option first with one line on why. I can always
  answer in free text. Use a written, numbered list only where that tool does
  not exist. Do not ask about anything you can decide and undo yourself.
```

`AskUserQuestion` already does the hard part: up to four questions per call,
two to four options each, an "Other" choice for free text that is always there,
a note on your pick, and previews for comparing mockups or code. Its own
guidance says to put the recommended option first, marked "(Recommended)".

If one instruction file feeds several harnesses, name each one's tool in the
rule. Mine: `AskUserQuestion` in Claude Code, an `ask_user_question` tool in
Topics, `ask_user` in OpenClaw (one question, up to four options, answered with
a reaction on WhatsApp). jcode has none, so the written list stays there.

## Method and privacy

The history menu is a local replay: for every input, what it would have shown
at that moment, checked against what I actually sent. The judge was blind, with
a shuffled control; the labels had a blind second pass (kappa 0.61). The
options eval cost $1.02 (255k tokens), the corrections study $1.41 (587k).

Every model call was isolated, so the study left no sessions or history behind
to pollute the next count. Run it from an empty folder, so no project
`CLAUDE.md` or memory gets in:

```bash
env -i HOME="$HOME" USER="$USER" PATH="$PATH" \
  CLAUDE_CODE_DISABLE_AUTO_MEMORY=1 CLAUDE_CODE_DISABLE_CLAUDE_MDS=1 \
  CLAUDE_CODE_SKIP_PROMPT_HISTORY=1 \
  claude -p --no-session-persistence --tools '' --disable-slash-commands \
    --strict-mcp-config --setting-sources project \
    --settings '{"disableAllHooks":true}' < prompt.txt
```

After 36 calls like this, none of their run ids turned up under `~/.claude`.

The scripts ran on my machine. The only model that saw message text was
Claude, through my own subscription; nothing went to free or third-party
models. Raw texts were deleted after labelling, and what is left is labels and
counts. This page has aggregates only, plus a few stock replies such as "go".
