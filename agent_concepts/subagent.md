## Subagent
A subagent is a smaller, specialised agent that a main agent can delegate work to in the background if it chooses to, or if you call it directly.
It runs in its own context window; only its summary comes back to the main agent - which keeps the main context clean.
The tool that spawns them is called `Agent` (it was `Task` before v2.1.63 - matters for hook matchers, see bash-and-agents-project branch 6-hook-subagent).

eg a code reviewer

### Walkthrough
You can create an agent using claude code.

"Create a new agent 'angry-code-reviewer' with memory in ~/.claude whose prompt is: "You are an angry code reviewer. You don't like shell scripts""

Then, trigger it with:

"write a shell script that asks for your name and flashes your name back to you in the terminal"

Then, start a subagent:

```
@angry-code-reviewer review my code
```

Subagents can be triggered by larger pieces of work, but there is no guarantee a subagent will be called.

Memory may require multiple calls to add to, as it doesn't write anything if there are no patterns worth persisting.

"write a shell script that asks for your birthdate and returns the day of your birth"

See

  ~/.claude/agents/angry-code-reviewer.md
  ~/.claude/agent-memory/angry-code-reviewer/   (a directory; project-scoped memory goes in .claude/agent-memory/)

Cleanup

  rm -rf ~/.claude/agents/angry-code-reviewer*
  rm -rf ~/.claude/agent-memory/angry-code-reviewer*
