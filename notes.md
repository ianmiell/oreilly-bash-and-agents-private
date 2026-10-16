## COURSE KEY DETAILS

"In this course with Ian Miell, developers and technical practitioners will learn how AI coding agents use shell and command-line tools in real work and how Claude Code uses tools, commands, skills, hooks, plug-ins, subagents, and monitors. Through live demonstrations and practical exercises, you’ll create a simple skill, add a script to that skill, configure hooks to log shell and tool use, and examine the security implications of allowing an AI agent to execute commands."

Course submission:
  https://docs.google.com/document/d/1puS896JkDgNtpo41cLRjMpq1qkRvRuXhSRl7w-PllPY/edit?tab=t.0

Presentation:
  https://docs.google.com/presentation/d/1oXzgPDM7NVjt3ePTac_iplPZiemizh4z0pMSqlHmF74

If you have time left over!

/Users/imiell/git/shell-with-genai-oreilly

## Before course

### Switch off autosuggestions in ~/.zshrc

```
#plugins=(git zsh-autosuggestions zsh-syntax-highlighting)
plugins=(git zsh-syntax-highlighting)
#plugins=(git kubectl kube-ps1 minikube zsh-autosuggestions gcloud zsh-syntax-highlighting)
### fzf and zsh-autocomplete
#[ -f ~/.fzf.zsh ] && source ~/.fzf.zsh
#source /opt/homebrew/share/zsh-autosuggestions/zsh-autosuggestions.zsh
```



### Getting


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



### Close other apps

### Mute phone

## LLM Architectures:
    https://medium.com/@damianvtran/the-anatomy-of-a-modern-llm-0347afd72514
    https://magazine.sebastianraschka.com/p/the-big-llm-architecture-comparison
    https://sebastianraschka.com/llm-architecture-gallery/
  Not covered here, but interesting

## Shell and Agents
The `.claude` folder
  Where all the claude related stuff is. There's one in your home folder, and in each folder you've spun claude up in.
  claude sets the ~/.gitignore file to ignore .claude/settings.local.json files, so it doesn't get added to git

Searching
  grep / rg
  find / fd

Data preparation:
  Uses fiddly commands to carry out user demands, eg:
    sed
    awk

API calling commands:
  az
  aws
  gcloud
  Docker
  kubectl
  terraform
  helm

JSON:
  How AI uses JSON under the hood
  jq .
  AI tools often produce fragments: a command, a script, a file, a config, a report, a test run. Shell scripting turns those fragments into repeatable workflows.
  UNIX principle - building tools together

## Links:
  https://claude.com/blog/how-claude-code-works-in-large-codebases-best-practices-and-where-to-start
  https://joe.dev/posts/nine-kinds-of-agents/
