## Skills
    https://support.claude.com/en/articles/12512176-what-are-skills

## Links
https://www.llmskills.directory/
https://imjaredz.com/writing/llm-idioms.html

### Walkthrough

```
cd ~/git/bash-and-agents-project
git switch 1-helloworld-simple
```

How does a skill get triggered automatically?
  The application internally asks the model which skills look appropriate. If it gets a certain response, it will load up the skill into the context.
  It's not entirely predictable, but you can reduce the risk of failure:
    ❯ say hello
  ⏺ Hello! How can I help you today?
  ✻ Churned for 5s
  ❯ say hello world to me
  ⏺ Skill(helloworld)
    ⎿  Successfully loaded skill

To clear context since agent loaded:

```
/clear
```

Skills hot-reload - edits to SKILL.md are picked up without restarting. There is no /reload.
If you create a new top-level skills directory mid-session:

```
/reload-skills
```

The skill in 1-helloworld-simple has no frontmatter. That still works (name = directory name, description = first line), but a `description:` in frontmatter is what the model matches on, so it makes triggering more reliable:

```
---
name: helloworld
description: Says hello world. Use when the user asks you to say hello.
---
```

Adding scripts:

```
cd ~/git/bash-and-agents-project
git switch 2-helloworld-script
```

If a skill is not 'chosen' by the harness, you can call it as a 'Command'

```
/helloworld
```


### Skill patterns:

#### Tell the skill who it is: `You ARE the agent loop.`

#### Roles: `You play the four LLM roles yourself; each role's instructions live in `roles/<role>.md``

Effort:
`# Role: fixer  (effort: low) — apply the fixes`

#### Arguments section (relate to shell):

```
#### Arguments
When invoked as `/lisa <owner/repo> <branch> [prompt-file]`, use them as
`LISA_REPO`, `LISA_BRANCH` and (implement mode only) the feature spec. Given a PR URL
or `owner/repo#N` instead, take the repo from it and the branch from
`gh pr view <url> --json headRefName`. Set `LISA_BASE` only if the user named a
non-`main` base; for a stack, keep it at the trunk (the bottom PR's base). If
repo/branch are missing, ask for them.
```
- Steps:

```
## 0 - first step
....
## 1 - second step
```

#### Embed code

```
Up to `N` passes (default 4). Each pass: **reviewer** → **thinker** → **fixer**.
```
for pass in 1..N:
  read roles/reviewer.md ; produce {verdict, findings[]}
  if findings: record them -> `lib/lisa notes add selfreview` (so they're never lost / re-raised)
  if verdict==approve or no findings: stop (done; deferred findings live in the note)
  read roles/thinker.md  ; classify findings fix|dismiss
  if nothing fix-worthy: stop
  read roles/fixer.md    ; apply fixes -> `lib/lisa commit` ; record `lib/lisa notes add addressed`
  if commit was "nochange": stop
```
```

#### Pre-requisites?

```
## Install

Prerequisites: `gh` (authenticated), the `github/gh-stack` and `agynio/gh-pr-review`
extensions, `git`, `jq`, and Python 3.9 or later.
```

#### Layout

```
## Layout

```
bin/ccfr              deterministic helper CLI (stack, size, classify, publish, ...)
lib/ccfr/             its Python modules (stdlib only)
config/packs.json     packs, ID prefixes, detection, generated-file globs
config/contracts.json cross-repo producer -> consumer edges
rules/<pack>/         shared rules        rules/repo/<repo>/  single-repo rules
skills/review/        /ccf-review:review
skills/rules/         /ccf-review:rules
calibration/          frozen cases for measuring run-to-run agreement
docs/                 severity scale and rule format
```
```


#### Rules

From README/AGENTS/SKILL etc:
```
- **Severity comes from rules.** Findings cite a rule, and the rule fixes the level.
  Findings without a rule go through a fixed decision tree. See
  [docs/severity.md](docs/severity.md).
```

```
---
id: CORE-CI-001
title: Workflow uses mutable action refs or broad token permissions
severity: must-fix
status: active
applies_to: [".github/workflows/*"]
---
## Why
Mutable action tags can be repointed to malicious code, and default token scopes give every job write access.

## Detect
In `.github/workflows`, third-party actions pinned by tag or branch instead of a commit SHA, missing top-level `permissions:`, or `actions/checkout` without `persist-credentials: false`.

## Fix
Pin actions to full commit SHAs (keep the tag as a comment), set `permissions: contents: read` at the top and widen per job only where needed.
```


#### Develop / update a skill

```
## Develop

```bash
python3 -m unittest discover -s ccf-review/tests
ccf-review/bin/ccfr rules lint
```
```
