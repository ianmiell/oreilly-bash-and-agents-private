### Plugins

- What is a plugin?
  - Plugins package hooks, skills, agents (?), MCP Servers (tools), LSP Servers (Language Server Protocol), Monitors,

Plugins have scopes: user, project, local, managed

Plugins give you a namespace for plugin calls, eg /myplugin:hello

#### Creating a plugin

Plugin typically has a README.md. [IS THIS REQUIRED/STANDARD?]

TODO show how to create a plugin
https://code.claude.com/docs/en/plugins#convert-existing-configurations-to-plugins

TODO:
What is the .claude-plugin folder?


##### Add a plugin

- 1:

.claude-plugin/marketplace.json

```
{
  "name": "ccf-skills",
  "owner": {
    "name": "compliance-framework"
  },
  "plugins": [
    {
      "name": "ccf-review",
      "source": "./ccf-review",
      "description": "Consistent, rule-based PR review for every CCF repo: stack-aware briefs in the console, rule-cited findings with fixed severities, inline review threads."
    },
    {
      "name": "lisa",
      "source": "./lisa",
      "description": "Autonomous PR author + fixer loop: implement, adopt, or keep fixing review feedback and CI on a PR or a whole PR stack until approved; optionally as the ccf-lisa GitHub App bot."
    }
  ]
}
```

- 2:

Upload to github


- 3:

```
/plugin marketplace add <github-username>/<repository-name>
```

##### Using a plugin

`/personal-assistant:mail` - use mail skill from personal-assistant plugin
