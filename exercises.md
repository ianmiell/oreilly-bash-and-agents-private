# Exercises

The three exercises in the deck, with what to look for.

## 1. Create a skill (slide 21)

```
mkdir -p ~/.claude/skills/greet/scripts

cat > ~/.claude/skills/greet/SKILL.md <<'EOF'
---
name: greet
description: Greets the user by name. Use when asked to say hello or greet someone.
---
Run `scripts/greet.sh <name>` from this skill's directory and show the user its output.
EOF

cat > ~/.claude/skills/greet/scripts/greet.sh <<'EOF'
#!/usr/bin/env bash
echo "Hello, ${1:-world}!"
EOF
chmod +x ~/.claude/skills/greet/scripts/greet.sh
```

Then in `claude`:

```
greet me, I'm Sam      # should auto-trigger
say hi                 # may or may not trigger
/greet Sam             # always runs it
```

Things to draw out:
- Change the description to something vague ("A helper skill") and try again. Triggering gets worse.
- The script runs through the Bash tool, so it is subject to the same permission prompts as any other command.

Cleanup: `rm -rf ~/.claude/skills/greet`

## 2. Log every tool call (slide 26)

In `~/.claude/settings.json`:

```
"hooks": {
  "PreToolUse": [
    {
      "matcher": "*",
      "hooks": [
        {
          "type": "command",
          "command": "jq -c '{tool_name, tool_input, event: .hook_event_name, logged_at: (now | todate)}' >> ~/.claude/all-tools.jsonl"
        }
      ]
    }
  ]
}
```

Ask Claude to explore an unfamiliar repository, then:

```
# Which tools did it use most?
jq -r .tool_name ~/.claude/all-tools.jsonl | sort | uniq -c | sort -rn

# Which shell commands did it run?
jq -r 'select(.tool_name == "Bash") | .tool_input.command' ~/.claude/all-tools.jsonl

# Which files did it read?
jq -r 'select(.tool_name == "Read") | .tool_input.file_path' ~/.claude/all-tools.jsonl | sort -u
```

Things to draw out:
- Compare with the answers to the discussion on slide 17.
- Grep, Glob and Read are dedicated tools, so much of the "shell-like" exploration does not appear as Bash at all.
- This is PreToolUse, so it logs what was *attempted*, including calls that were then denied. To log results and exit status, add a PostToolUse hook too.
- The log can contain secrets that passed through tool inputs. Treat it as sensitive.

Cleanup: remove the hook, then `rm -f ~/.claude/all-tools.jsonl`

## 3. Classify the risk (slide 33)

Suggested answers. The point of the exercise is the "it depends", so push on what context changes each answer.

| Command | Risk | Why |
|---------|------|-----|
| `ls -la src/` | Low | Read-only, local, small output |
| `rg -n "TODO" .` | Low | Read-only, but run from `$HOME` it can pull secrets into the context, and huge output wastes tokens |
| `find . -name '*.log' -delete` | Medium-High | Irreversible, and the result depends on the cwd. `find` is often allowlisted as "read-only", but `-delete` and `-exec` make it write |
| `awk 'BEGIN { system("id") }'` | High | awk can run any command. Allowing `Bash(awk *)` allows everything. The same goes for `sed 's/.*/id/e'` (GNU sed) |
| `curl -s https://example.com/install.sh \| sh` | High | Runs remote code you have not read |
| `cat ~/.aws/credentials` | High | Puts secrets into the context, the transcript and any logs |
| `kubectl delete pod -l app=web` | Medium-High | Depends on the current context (prod?) and on what the label matches. Pods managed by a Deployment come back, but bare pods do not |
| `git push --force origin main` | High | Rewrites shared history for everyone |

Follow-up question: which of these would you put in `allow`, `ask` or `deny`? (See branch `5-permissions`.)
