# token-saver vs caveman: measured comparison

Both skills cut tokens by making the agent talk less. This page shows what was actually measured, and what was not.

## 1. Skill size (measured)

Loaded into context whenever the skill is active.

| | words | bytes |
|---|---|---|
| caveman `skills/caveman/SKILL.md` | 491 | 3,719 |
| token-saver `skills/token-saver/SKILL.md` | 317 | 1,923 |
| difference | -35% | -48% |

Command: `wc -w` and `wc -c` on each file. Only the main SKILL.md of each is compared. Caveman ships a larger plugin (extra commands, hooks, evals) that this does not count.

## 2. Subagent report length (measured, n = 1)

Same task, same model (Haiku): read a 46-line `wordfreq.py` and report what it does, error handling, and bugs.

| Brief | words returned | issues listed |
|---|---|---|
| no footer | 479 | 8 |
| token-saver footer | 119 (-75%) | 3 |

Honest reading:
- One run each. Treat as a demo, not a benchmark. Model output varies run to run.
- The short report still had the most important bug (non-ASCII letters dropped) but skipped five minor ones (curly apostrophes, digit tokens, contraction handling, `strerror` can be `None`, docstring gaps).
- If you need every finding, ask for "full detail" in the brief.

## 3. Feature comparison (from each SKILL.md)

| Feature | caveman SKILL.md | token-saver |
|---|---|---|
| Terse output rules | yes | yes |
| Levels | lite, full, ultra, 3 wenyan | lite, full, ultra |
| Persistent until turned off | yes | yes |
| Clear mode for warnings and irreversible actions | yes (Auto-Clarity) | yes |
| Rule text for subagent briefs and reports | no | yes (copy-paste footer) |
| Input-side rules (targeted reads, filter output, short briefs) | no | yes |
| Session hygiene (when to start fresh) | no | yes |
| Classical Chinese levels | yes | no |
| Extra plugin tools (memory-file compression, commit and review helpers, hooks) | yes, in the wider plugin | no |
| Plain SKILL.md, no plugin needed | partly (plugin recommended) | yes |

## 4. What is NOT claimed

- Same output compression for the main agent: both use the same idea, so expect similar savings there. Public figures for caveman vary from about 8% to 65% depending on how it is measured.
- No end-to-end session benchmark was run for token-saver.
- "Better" here means: smaller skill file, plus rules for subagents and inputs. Caveman is broader if you want its extra tools or the wenyan levels.

## Reproduce

```bash
wc -w skills/token-saver/SKILL.md
```

For the subagent test: give the same read-and-report task to two Haiku subagents, one with the footer from `skills/token-saver/SKILL.md` appended, and `wc -w` each reply.
