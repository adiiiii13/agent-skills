---
name: boss-ultra
description: Run a task with a team of agents AND maximum token saving in one command. Combines the handle-boss model routing (Haiku for basic work, Sonnet for hard work, Opus only with the user's permission) with token-saver ultra output. Invoke with /boss-ultra <task>. Use when the user types /boss-ultra or wants the cheapest possible full run. Works standalone; needs no other skill installed.
license: MIT
metadata:
  author: adiiiii13
  version: "1.0.0"
---

# /boss-ultra

= handle-boss (who does the work) + token-saver ultra (how little is said). Both always on. Standalone.

## Models

| Tier | Work | Claude Code `Agent` model | Others |
|------|------|---------------------------|--------|
| Haiku 5.5 | files, rename, folders, boilerplate, small edits, run commands | `haiku` | cheapest model |
| Sonnet 5.5 | design, multi-file logic, debugging, backend | `sonnet` | default model |
| Opus 5.5 | last resort, user permission required | `opus` | strongest model |

No per-subagent model choice available? Say so once; split work by effort instead.

## Flow
1. **Understand.** One line. Ask only if a wrong guess is costly; else state assumption, go.
2. **Plan.** 3-7 steps, each `basic` or `hard`.
3. **Design** -> Sonnet writes a short bullet spec.
4. **Scaffold** -> Haiku, from the spec.
5. **Build** -> Sonnet for logic, Haiku for repetitive parts.
6. **Verify** -> Haiku runs tests/commands; Sonnet fixes failures.
7. **Report.**

Do a job in one tool call yourself instead of spawning a subagent. One subagent per step.

## Ultra tokens (always)
- Output: drop articles, filler, hedging, pleasantries. Abbreviate (fn, cfg, req). Arrows for cause (A -> B). One word if enough. Code, paths, errors, numbers exact.
- Every brief = goal, paths, spec bullets, done-when. Paths, never pasted files. End it with:
```
Reply: terse, max 8 lines, no greeting or recap.
Report only: paths changed, result, exact errors. Quote 5 lines max, never whole files or logs.
```
- Read line ranges, not whole files. Filter command output (`tail`, `grep`). Do not re-read what a subagent just wrote; verify by diff or test.
- Full sentences only for: Opus question, security warnings, irreversible actions (delete, push, deploy, send), or when the user asks to explain.

## Announce (always)
Before each subagent: `-> [HAIKU] <job>` / `-> [SONNET] <job>` / `-> [OPUS - approved] <job>`.
After: `<- [MODEL] done: <one line>` or `failed: <why>`. Own work: `[MAIN]`.

## Escalation
Haiku wrong after 1 retry -> Sonnet (no ask).
Sonnet failed 2+ real attempts on one problem -> STOP. Plain words: what failed, what was tried, "Opus 5.5 use karun? (haan/nahi)". Only a clear yes unlocks Opus, for that problem only. No -> report blocker and options.

## Rules
No scope creep. Ask before send, delete, deploy, push. No "done" without evidence.

## Report
```
Status: Done | Partly | Blocked
Result: <1-2 lines>
Evidence: <run command, test output, paths>
Model log:
  1. [SONNET] <job> - done
  2. [HAIKU] <job> - done
  Opus: not used | [OPUS, approved] <job>
Needs from you: <decision | nothing>
```
