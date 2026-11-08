---
name: handle-boss
description: Complete a task end to end using cheap models for easy work and strong models only for hard work. Invoke with /handle-boss <task>. Haiku does basic work (file ops, boilerplate, structure, commands), Sonnet does hard work (design, multi-file logic, debugging), Opus is used only after Sonnet fails repeatedly and only with the user's permission. Use when the user types /handle-boss or says "boss task".
license: MIT
metadata:
  author: adiiiii13
  version: "2.1.0"
---

# /handle-boss

Run the user's task as a team of agents. Cheapest model that can do each piece. You decide the routing yourself, without asking, except for Opus.

## Model tiers

| Tier | Work | Claude Code (`Agent` tool `model`) | Codex / OpenCode / others |
|------|------|------------------------------------|---------------------------|
| Haiku 5.5 | basic: open/read/list files, rename, small edits, folder + boilerplate structure, run command and report | `haiku` | smallest/cheapest model |
| Sonnet 5.5 | hard: design, architecture, multi-file logic, debugging, backend/API logic | `sonnet` | default/mid model |
| Opus 5.5 | last resort, needs user permission | `opus` | strongest model |

If the agent cannot pick a model per subagent, say so once, then follow the same split by effort: do mechanical work tersely, think hard only where needed.

## Flow

1. **Understand.** Restate the task in one line. Ask the user only if a wrong guess is costly; otherwise state assumptions and go.
2. **Plan.** Break into pieces. Label each `basic` or `hard`. Show the plan in a few lines.
3. **Design (Sonnet).** Hard thinking first: structure, data model, API, key decisions. Output a short written spec the others follow.
4. **Scaffold (Haiku).** From the spec, Haiku creates folders, boilerplate, config, empty modules, simple files.
5. **Build (Sonnet for logic, Haiku for repetitive parts).** Core logic and tricky code to Sonnet; copy-paste style or trivial pieces to Haiku.
6. **Run and verify.** Haiku runs build/tests/commands and returns a short result. Sonnet reads failures and fixes.
7. **Report.** See below.

Give each subagent a self-contained brief: goal, files, spec, done-when. It knows only what you write. Check its output (diff, test result) before moving on.

## Example

`/handle-boss build a website with a backend`
- Sonnet: design pages, API routes, data model -> spec
- Haiku: create project structure, install deps, boilerplate files
- Sonnet: implement backend logic and tricky UI parts
- Haiku: run the app and tests, report errors
- Sonnet fixes errors. Repeated failures -> ask permission for Opus

## Announce every delegation (always)

The user must always see which model is doing what. Before EVERY subagent call, print one line:

```
-> [HAIKU] creating project folders and boilerplate files
-> [SONNET] designing the API routes and data model
-> [OPUS - approved by you] fixing the auth bug Sonnet failed on twice
```

Format: `-> [MODEL] <exact job in plain words>`. One line per subagent, never batched, never skipped, even for tiny jobs.

After EVERY subagent returns, print one line: `<- [MODEL] done: <what it did, one line>` (or `failed: <why>`).

Keep a running log. In the final report, list every step line by line (see below). Work you do yourself in the main session is labelled `[MAIN]`.

## Escalation to Opus (permission gate)

Only when Sonnet failed or crashed 2+ real attempts on the same problem. Then STOP and tell the user in plain words:
- what failed
- what Sonnet tried
- "Opus 5.5 use karun? (haan/nahi)"

Wait for a clear yes. The yes covers that problem only. Never use Opus silently. If the user says no, report the blocker and options instead.

Haiku wrong after one retry -> move that piece up to Sonnet (no permission needed).

## Rules
- Do not expand scope. Note extras instead of building them.
- Ask before anything outward-facing or irreversible (send, delete, deploy, push).
- Never claim done without evidence.

## Final report (short, boss-readable)

```
Status: Done | Partly done | Blocked
Result: <what was built, 1-2 lines>
Evidence: <how to run it, test output, file paths>
Model log (line by line):
  1. [SONNET] <job> - done
  2. [HAIKU]  <job> - done
  3. [SONNET] <job> - failed, retried
  4. [OPUS, approved] <job> - done   (omit if unused; write "Opus: not used")
Needs from you: <decision or nothing>
```
