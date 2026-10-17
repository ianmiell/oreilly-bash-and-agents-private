# Bash and Agents - course run-sheet

2-hour O'Reilly live course. Notes for the instructor, not for learners.

## Links

- Proposal: https://docs.google.com/document/d/1puS896JkDgNtpo41cLRjMpq1qkRvRuXhSRl7w-PllPY/edit
- Deck (original): https://docs.google.com/presentation/d/1oXzgPDM7NVjt3ePTac_iplPZiemizh4z0pMSqlHmF74
- Deck (improved draft, 39 slides): https://docs.google.com/presentation/d/1sVAP668qByqLiKG9iO7A7vaBJptvmfqBFF3nFEboh9A
- Demo repo (public): https://github.com/ianmiell/bash-and-agents-project
- Exercises and suggested answers: [exercises.md](exercises.md)

## Agenda

Slide numbers refer to the improved draft deck.

| Time | Part | Slides | Demo / activity | Notes file |
|------|------|--------|-----------------|------------|
| 0:00-0:10 | Intro: hook, who am I, why shell, poll, outline | 1-6 | Poll: CLI tools | [context_engineering.md](context_engineering.md), [typical_commands.md](typical_commands.md) |
| 0:10-0:30 | I: How agents work | 7-17 | Discussion: exploring a new codebase | [definitions.md](definitions.md), [notes.md](notes.md) |
| 0:30-0:50 | II: Skills and scripts | 18-21 | Branches `1-helloworld-simple`, `2-helloworld-script`; exercise: create a skill | [agent_concepts/skills.md](agent_concepts/skills.md), [agent_concepts/commands.md](agent_concepts/commands.md) |
| 0:50-0:55 | Break | 22 | | |
| 0:55-1:15 | III: Hooks and observability | 23-26 | Branches `3-hook-bash`, `6-hook-subagent`; exercise: log every tool call | [agent_concepts/hooks.md](agent_concepts/hooks.md) |
| 1:15-1:25 | IV: Command design | 27-29 | Branch `7-command-design` | [command_design.md](command_design.md) |
| 1:25-1:45 | V: Security | 30-33 | Branches `4-security`, `5-permissions`; exercise: classify the risk | [security.md](security.md) |
| 1:45-1:55 | VI: Extending Claude Code | 34-36 | angry-code-reviewer subagent; `/ok` + `/argh` monitors | [agent_concepts/subagent.md](agent_concepts/subagent.md), [agent_concepts/monitors.md](agent_concepts/monitors.md), [agent_concepts/plugins.md](agent_concepts/plugins.md) |
| 1:55-2:00 | Takeaways, Q&A | 37-39 | | |

If running long, cut in this order: the Part VI demos (talk to slides 35-36 instead), then the command design walkthrough.

### Mapping to the proposal schedule

The proposal lists 8 sections and two breaks. The deck covers them as follows:

- Section 1 (Why agents need the shell): intro slides 2-5, discussion slide 17
- Section 2 (How Claude Code sees the world): slides 7-16
- Sections 3 and 4 (skills, then scripts): Part II
- Section 5 (hooks): Part III
- Section 6 (security): Part V
- Section 7 (tools, monitors, plugins, MCP): slides 12-15 and Part VI
- Section 8 (wrap-up): slide 37

Differences to resolve with O'Reilly, or in the deck:

- Command design (Part IV) is not in the proposal schedule. Either add it to the proposal or present it as part of Section 4.
- The proposal has a second break, before Section 7. The deck has one break. Add a break slide before Part V if needed.
- The proposal says "Create plugins from Claude infrastructure primitives". The deck only describes plugins (slide 36). [agent_concepts/plugins.md](agent_concepts/plugins.md) has the steps if you want to demo it.

## Before the course

- [ ] Switch off zsh autosuggestions (see [notes.md](notes.md))
- [ ] Close other apps, mute phone
- [ ] Clean demo state:
      `rm -f ~/env_copied ~/.claude/bash-commands.jsonl ~/.claude/all-tools.jsonl`
      `rm -rf ~/.claude/agents/angry-code-reviewer* ~/.claude/agent-memory/angry-code-reviewer* ~/.claude/skills/greet`
- [ ] Remove any course hooks from `~/.claude/settings.json` left over from rehearsal
- [ ] `claude --version`, and re-check anything version-sensitive below
- [ ] Run each demo branch once end to end

## Things to check in the demo repo

- `6-hook-subagent` matches on `"Task"`. The subagent tool was renamed `Agent` in Claude Code v2.1.63, with `Task` kept as an alias for permission rules. The docs do not say whether a hook matcher of `Task` still fires. Test it, and if it doesn't, change the matcher to `Agent` (or `Agent|Task`).
- `1-helloworld-simple` has a SKILL.md with no frontmatter. That still loads: the name comes from the directory and the description from the first line. It is worth pointing out that adding a `description:` makes auto-triggering more reliable. The exercise on slide 21 does this.
- There is no `/reload` command. Skills hot-reload. `/reload-skills` re-scans when a new skills directory appears mid-session, and `/reload-plugins` applies plugin changes.
