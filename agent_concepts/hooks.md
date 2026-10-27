## Hooks

https://code.claude.com/docs/en/hooks-guide

### Walkthrough
Add this to ~/.claude/settings.json

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


Questions:

- What is {logged_at: now | todate} doing?
  - adds the date of logging to the json

- How do you list the existing tools? ('matcher')
  - You can't. There is a reference: https://code.claude.com/docs/en/tools-reference
  - You can see that there is a mapping from shell and command line tools to many tools (Grep, Glob)

- How is data passed to the command?
  - Your hook receives the JSON once on stdin to the command

- How does a hook block something?
  - exit 0 = carry on
  - exit 2 = block, and stderr is fed back to Claude as the reason (only on events that can block, eg PreToolUse - on PostToolUse the tool has already run)
  - any other non-zero exit = non-blocking error
  - see branch 6-hook-subagent: block-subagent.sh

- How do tools manage permissions?
  - eg: '/clear; lookup the ls man page and give me the most interesting flags' - asks for permission for col -b.
  - .claude/settings.local.json in local folder

- What other hooks are there?
  - https://code.claude.com/docs/en/hooks
  - or /hooks in claude

To log all tool uses:

```
  "hooks": {
  "PreToolUse": [
    {
    "matcher": "*",
    "hooks": [
      {
      "type": "command",
      "command": "jq -c '{tool_name, tool_input, event: .hook_event_name}' >> ~/.claude/all-tools.jsonl"
      }
    ]
    }
  ]
  }
```

in ~/.claude/settings.json

Then analyse it:

```
jq -r .tool_name ~/.claude/all-tools.jsonl | sort | uniq -c | sort -rn
jq -r 'select(.tool_name == "Bash") | .tool_input.command' ~/.claude/all-tools.jsonl
```

PreToolUse logs what was attempted, including calls that were then denied. Add a PostToolUse hook to see results.

Worth reading: https://code.claude.com/docs/en/tools-reference

