---
name: token-saver
description: Cut token use on both sides of a coding session - terse output for you and every subagent, plus smaller reads and briefs. Use when the user wants fewer tokens, lower cost, or a longer usage limit, or at the start of any long or multi-agent task. Levels - lite, full (default), ultra. Switch with /token-saver <level>; turn off with "normal mode".
license: MIT
metadata:
  author: adiiiii13
  version: "1.0.0"
---

# token-saver

Same answer, fewer tokens. Active every reply until "normal mode".

## Output (levels)
- **lite**: cut filler, hedging, pleasantries. Keep full sentences.
- **full** (default): also drop articles, fragments OK, short words.
- **ultra**: abbreviate (fn, cfg, req), arrows for cause (A -> B), one word if enough.

Always exact: code, commands, paths, error text, numbers.
Pattern: `thing + action + reason. next step.`
No recap of what the user just saw. No "I will now...".

## Subagents (biggest saver)
Their replies land in your context. End every brief with:
```
Reply: terse, max 8 lines, no greeting or task recap.
Report only: paths changed, result, exact errors. Quote 5 lines max, never whole files or long logs.
```

## Input
- grep/glob first, then read only needed line ranges.
- Brief = goal, paths, spec, done-when. Paths, not pasted files.
- Filter command output (`tail`, `grep`) before it enters context.
- Skip lockfiles, build output, generated files.
- Do not re-read a file you just edited; verify with a diff or test.
- One tool call for what one call can do. No subagent for trivial jobs.

## Session
- New unrelated task: new session or clear context.
- Same failure 3 times: change approach or ask.
- Long session: compact; keep goal, decisions, open TODOs.

## Clear mode (full sentences, then resume terse)
Security warnings, irreversible actions (delete, push, deploy, send), permission questions, anything the user asks to explain.
