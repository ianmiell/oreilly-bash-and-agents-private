# Bash and Agents - course presenter notes
2-hour O'Reilly live course. Notes for the instructor, not for learners.

## Before the course

- [ ] Switch off zsh autosuggestions (run 'autosuggest-disable')
- [ ] Close other apps
- [ ] Mute phone
- [ ] Close all claude sessions
- [ ] Clean demo state:
      `rm -f ~/env_copied ~/.claude/bash-commands.jsonl ~/.claude/all-tools.jsonl`
      `mv ~/.claude ~/.claude.bak && claude`
- [ ] `claude --version`, and re-check anything version-sensitive below

## Links
- Deck (improved draft, 39 slides): https://docs.google.com/presentation/d/1sVAP668qByqLiKG9iO7A7vaBJptvmfqBFF3nFEboh9A
- Demo repo (public): https://github.com/ianmiell/bash-and-agents-project

## Agenda
### INTRO
SLIDES 1-6
- AI uses shell and the command line to quickly inspect things, just like people do

### HOW AGENTS WORK
SLIDES 7-17
Agent
  How the agent does this is its secret sauce (if it's not open source). It might add data to your "prompt" before it dispatches is to the LLM or decide to run a script - it often uses shell to achieve its goals.
  Specifically a claude agent (in .claude/agents)
Harness
  Program that manages input and output to an LLM
  It's just a program. The program manages how the LLM is called and what to do with its outputs.
  But these programs follow certain patterns (which brings us to other agent concepts).
Tools
  Weather json example
  Harness executes and returns json. That JSON goes back to the LLM, which may call another tool, or turn it into a human response.
  Agent tells the LLM through context what tools are available.
  Tools can be internal (to claude) and external (MCP Server)
    internal: https://code.claude.com/docs/en/tools-reference
  'Skill' is an internal tool designed to execute skills where needed.
Question
  Shell as gatherer of context
  Go to compliance-framework/api and claude "find code related to networking in the api"
  eg
    sed -n '1,60p' internal/config/config.go | grep -n "allowedOrigins" | grep -n "allowedOrigins" -A5 -B5 internal/config/config.go | head -30


### SKILLS AND SCRIPTS
SLIDES 18-21
Branches `1-helloworld-simple`, `2-helloworld-script`; exercise: create a skill

https://support.claude.com/en/articles/12512176-what-are-skills

Skills hot-reload. If you create a new one in a session: reload-skills

The skill in 1-helloworld-simple has no frontmatter. That still works (name = directory name, description = first line), but a `description:` in frontmatter is what the model matches on, so it makes triggering more reliable:

---
name: helloworld
description: Says hello world. Use when the user asks you to say hello.
---

Can call a skill directly.
Can add an argument to a skill call, just as with shell scripts. Demo this if time.

### HOOKS AND OBSERVABILITY
SLIDES 23-26
Branches `3-hook-bash`, `6-hook-subagent`
exercise: log every tool call

#### Walkthrough
Add to ~/.claude/settings.json
```
  "hooks": {
    "PreToolUse": [
      {
      "matcher": "Bash",
      "hooks": [
        {
        "type": "command",
        "command": "jq -c '. + {logged_at: now | todate}' >> ~/.claude/bash-commands.jsonl"
        }
      ]
      }
    ]
  }
```

matcher - which tool?
hook    - the hook
type    - type of hook call (eg http, agent (spawn an agent), prompt)
command - takes json as input. can be script relative to the pwd

Here we use the jq command. This is a critical command

TODO: Brief introduction to jq?



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
