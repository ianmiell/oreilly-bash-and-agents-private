# Bash and Agents - course presenter notes
2-hour O'Reilly live course. Notes for the instructor, not for learners.

## Before the course

- [ ] Switch off zsh autosuggestions (see [notes.md](notes.md))
- [ ] Close other apps, mute phone
- [ ] Clean demo state:
      `rm -f ~/env_copied ~/.claude/bash-commands.jsonl ~/.claude/all-tools.jsonl`
      `rm -rf ~/.claude/agents/angry-code-reviewer* ~/.claude/agent-memory/angry-code-reviewer* ~/.claude/skills/greet`
- [ ] Remove any course hooks from `~/.claude/settings.json` left over from rehearsal
- [ ] `claude --version`, and re-check anything version-sensitive below
- [ ] Run each demo branch once end to end

## Links

- Proposal: https://docs.google.com/document/d/1puS896JkDgNtpo41cLRjMpq1qkRvRuXhSRl7w-PllPY/edit
- Deck (original): https://docs.google.com/presentation/d/1oXzgPDM7NVjt3ePTac_iplPZiemizh4z0pMSqlHmF74
- Deck (improved draft, 39 slides): https://docs.google.com/presentation/d/1sVAP668qByqLiKG9iO7A7vaBJptvmfqBFF3nFEboh9A
- Demo repo (public): https://github.com/ianmiell/bash-and-agents-project
- Exercises and suggested answers: [exercises.md](exercises.md)

## Agenda

Part | Slides | Demo / activity | Notes file |
### INTRO
SLIDES 1-6
```
context_engineering.md
typical_commands.md
```

### HOW AGENTS WORK
SLIDES 7-17
```
definitions.md
notes.md
```

### SKILLS AND SCRIPTS
SLIDES 18-21
Branches `1-helloworld-simple`, `2-helloworld-script`; exercise: create a skill
```
agent_concepts/skills.md
agent_concepts/commands.md
```

### HOOKS AND OBSERVABILITY
SLIDES 23-26
Branches `3-hook-bash`, `6-hook-subagent`
exercise: log every tool call
```
agent_concepts/hooks.md
```


### COMMAND DESIGN
SLIDES 27-29
Branch `7-command-design`
command_design.md

### SECURITY
SLIDES 30-33
Branches `4-security`, `5-permissions`
exercise: classify the risk
```
security.md
```

### EXTENDING CLAUDE CODE
SLIDE 34-36
angry-code-reviewer subagent; `/ok` + `/argh` monitors
```
agent_concepts/subagent.md
agent_concepts/monitors.md
agent_concepts/plugins.md
```
If running long, cut in this order: the Part VI demos (talk to slides 35-36 instead), then the command design walkthrough.

### Takeaways, Q&A
SLIDES 37-39



