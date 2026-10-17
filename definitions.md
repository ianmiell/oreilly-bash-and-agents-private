Define some terms related to what we are discussing.
Any terms that confuse people?

#### High Level Concepts
LLM
  The 'thing' that takes input (context) and produces output (simple view - internals of LLMs can get much more complex)
Harness
  Program that manages input and output to an LLM
  It's just a program. The program manages how the LLM is called and what to do with its outputs.
  But these programs follow certain patterns (which brings us to other agent concepts).
Agent
  Agent = LLM + Harness
  How the agent does this is its secret sauce (if it's not open source). It might add data to your "prompt" before it dispatches is to the LLM or decide to run a script - it often uses shell to achieve its goals.
  Specifically a claude agent (in .claude/agents)
Agentic AI
  AI that can pursue a goal by independently deciding what actions to take, using tools, observing the results, and adapting its approach—often across multiple steps—with limited human direction.
Tools
  LLMs ask for the harness to run a tool using JSON
  eg for a weather tool, tool is defined as:
    ```
    {
      "name": "get_weather",
      "description": "Get the current weather",
      "parameters": {
        "location": {"type": "string"},
        "units": {"type": "string"}
      }
    }
    ```
  Example call and response:
    ```
    {
      "name": "get_weather",
      "arguments": {
        "location": "London",
        "units": "celsius"
      }
    }

    {
      "temperature": 18,
      "conditions": "light rain"
    }
    ```
  Harness executes and returns json. That JSON goes back to the LLM, which may call another tool, or turn it into a human response.
  Agent tells the LLM through context what tools are available.
  Tools can be internal (to claude) and external (MCP Server)
    internal: https://code.claude.com/docs/en/tools-reference
  'Skill' is an internal tool designed to execute skills where needed.

Agent loop
  Harness sends context -> LLM replies with text or a tool call -> harness checks permissions and runs the tool -> output is appended to context -> repeat until the LLM answers.
  Every tool output costs context, which is why agents use `head`, `grep`, `sed -n` etc.

Context window
  Everything the LLM sees in one call: system prompt, CLAUDE.md, tool definitions, conversation, tool output.
  /clear empties it, /compact summarises it.

Commands
  /name - a markdown prompt the user invokes explicitly. See agent_concepts/commands.md
  Now merged into skills: .claude/commands/x.md and .claude/skills/x/SKILL.md both create /x

Hooks
  Commands the harness runs on lifecycle events (PreToolUse, PostToolUse, UserPromptSubmit, Stop, SessionStart... 30+ in all).
  Deterministic - the model doesn't decide whether they run. Event JSON on stdin; exit 2 blocks (where the event can be blocked). See agent_concepts/hooks.md

Plugins
  Packages of skills, commands, agents, hooks, MCP servers, LSP servers and monitors, shared via marketplaces. See agent_concepts/plugins.md

Skills
  A folder with SKILL.md (+ optional scripts, references). Name and description are always in context; the body is loaded when the model decides it's relevant, or when invoked as /name. See agent_concepts/skills.md

Subagent
  A delegated worker with its own prompt, tools and context window - only its summary comes back to the main agent. Defined in ~/.claude/agents or .claude/agents. See agent_concepts/subagent.md

Monitor
  Built-in tool that runs a command in the background and feeds each output line back to Claude. See agent_concepts/monitors.md

MCP
  Model Context Protocol - how external tools and data sources are exposed to an agent.
