---
name: tool-forge
description: Driving a website or API by hand for the third time (same request, same selector, browser calls in series)? Say so in one line and build a tested draft tool, a script or an MCP tool, that a human promotes. Drafting is free and reversible; promotion is not yours.
origin: Attilio's Jarvis setup, October 2026. A weekly distiller found these patterns after the fact, from memory; this skill catches them while the work is happening.
---

# Tool forge: from manual work to a tool

One travel-planning conversation made 198 browser calls on a hotel site and a car-rental site,
running the same kind of search by hand again and again. What fixed it was two small tools, not
a better prompt. This skill makes the agent notice the pattern while it works, say so, and draft
the tool itself.

## When it fires

Any of these, in the same session:

- the **same operation on a site three or more times** by hand: same URL with other parameters,
  same selector;
- **browser automation calls in series** to pull data out of pages;
- a calculation rebuilt by hand from fixed sources (fares, distances, timetables) that you would
  rebuild again next time.

Before proposing, **check that it does not exist already**: list your MCP servers and their tools,
disabled ones included, your drafts folder and your tool notes. It exists but is broken or
incomplete? Extend that one instead of building a second.

## What to say (one line, then do it)

> I've driven <site> by hand N times: I'm drafting a `<name>` tool in drafts. Promoting it is your call.

Drafting is reversible and free, so don't ask permission for it. If the answer the user is waiting
for is urgent, give it first and draft afterwards.

## Which shape

1. **A JSON or GraphQL API reachable with curl** (look at the page's network requests): a small
   script. The lightest option and the most stable one.
2. **Needs JavaScript, no heavy anti-bot**: a headless browser script, with the lightest engine that
   works (WebKit before Chromium), closed at the end of every run.
3. **Several operations on the same domain, or shared state** such as a reused browser: a tool in
   the MCP server that already covers that domain, or a new server, off by default.
4. **Needs judgement at every step**: that is a skill, not a tool. Write it down and stop.

## The draft

One folder per tool, in the drafts area your setup defines. Name it `^[a-z0-9][a-z0-9_-]{0,63}$`.

- `manifest.json`: `name`, `description`, `inputSchema`, `timeoutMs`. The description is one line:
  what it does, its input, its output, its limits. Keep `timeoutMs` below your tool host's own
  timeout.
- `run`, executable: JSON arguments on stdin, compact JSON on stdout, a non-zero exit with a
  readable message when it fails. Keep the output small: filter at the source, never raw HTML.
- `RATIONALE.md`: the pattern you saw (where, how many times), an example call, what can break.
- **Secrets only from the OS secret store**, read inside `run`. On macOS:
  `security find-generic-password -s <service> -a <account> -w`. Never in the folder.
- **No spending**: if the only API is a paid one, don't build the tool, write that down.

One host for folders like these is the
[`distilled` MCP server](https://github.com/zorahrel/jarvis-claudecode/tree/main/mcp-servers/distilled):
it serves every folder with a `manifest.json` and an executable `run` as a tool, ignores
folders whose name starts with `_` (so `_drafts/` stays invisible), and promotes a draft
only when a human runs `approve.sh <name>`, which scans it first.

## The bar (run it, don't describe it)

```sh
D=<drafts>/<name>
echo '<the real input that started this>' | "$D/run"; echo "exit=$?"   # 0 and real data
echo '{}' | "$D/run"; echo "exit=$?"                                  # non-zero, readable error
```

Paste the real output into `RATIONALE.md`. A draft never seen working on the real case is not a
draft.

## After

- Use the draft right away, from the shell, to finish the work in progress.
- Add one line to your tool notes: the name, what it does, "draft, awaiting approval".
- In your report: "Draft `<name>` is ready; to enable it, run <your promotion command>."
- **Never promote it yourself.** A tool added to an existing MCP server stays disabled until a
  human turns it on, and you say so.
