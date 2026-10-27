## COURSE KEY DETAILS

"In this course with Ian Miell, developers and technical practitioners will learn how AI coding agents use shell and command-line tools in real work and how Claude Code uses tools, commands, skills, hooks, plug-ins, subagents, and monitors. Through live demonstrations and practical exercises, you’ll create a simple skill, add a script to that skill, configure hooks to log shell and tool use, and examine the security implications of allowing an AI agent to execute commands."

Course submission:
  https://docs.google.com/document/d/1puS896JkDgNtpo41cLRjMpq1qkRvRuXhSRl7w-PllPY/edit?tab=t.0

Presentation:
  https://docs.google.com/presentation/d/1sVAP668qByqLiKG9iO7A7vaBJptvmfqBFF3nFEboh9A
  https://docs.google.com/presentation/d/1oXzgPDM7NVjt3ePTac_iplPZiemizh4z0pMSqlHmF74 (OLD!)

Fine-tuning permissions interactively:

```
# review my recent permission-giving, and ask me questions to update the permissions you can run without approval

...

# show me how you applied those rules

⏺ I applied them with one inline python3 script that edits ~/.claude/settings.json. This is exactly what ran:

  import json, os
  p = os.path.expanduser("~/.claude/settings.json")
  d = json.load(open(p))                                    # 1. read the whole existing file
  allow = d.setdefault("permissions", {}).setdefault("allow", [])   # 2. get (or create) permissions.allow
  new = [
    # Go checks
    "Bash(go test *)", "Bash(go build *)", "Bash(go vet *)", "Bash(gofmt -l *)",
    "Bash(go mod tidy)", "Bash(go mod download)", "Bash(go mod graph)",
    # UI checks
    "Bash(npx vitest *)", "Bash(npx vue-tsc *)", "Bash(npx eslint *)", "Bash(npx prettier --check *)",
    "Bash(npm ci)", "Bash(make reviewable)", "Bash(make lint)", "Bash(make test)", "Bash(make swag)",
    # Local git
    "Bash(git fetch *)", "Bash(git switch *)", "Bash(git checkout *)", "Bash(git branch *)",
    "Bash(git worktree add *)", "Bash(git add *)", "Bash(git commit *)",
    # Files, scripts, containers
    "Bash(mkdir *)", "Bash(cp *)", "Bash(python3 *)", "Bash(awk *)",
    "Bash(docker compose *)", "Bash(docker exec *)",
  ]
  added = [r for r in new if r not in allow]                 # 3. skip rules already present
  allow.extend(added)                                       # 4. append, keeping existing rules in order
  json.dump(d, open(p, "w"), indent=2); open(p, "a").write("\n")   # 5. write the whole file back
  print("added", len(added), "-> total allow rules", len(allow))
```

## LLM Architectures:
    https://medium.com/@damianvtran/the-anatomy-of-a-modern-llm-0347afd72514
    https://magazine.sebastianraschka.com/p/the-big-llm-architecture-comparison
    https://sebastianraschka.com/llm-architecture-gallery/
  Not covered here, but interesting

## Links:
  https://claude.com/blog/how-claude-code-works-in-large-codebases-best-practices-and-where-to-start
  https://joe.dev/posts/nine-kinds-of-agents/
