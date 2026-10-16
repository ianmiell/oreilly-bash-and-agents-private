Make output machine-readable (LLMs are trained on JSON)
They can also use jq to parse the output
Name the commands and flags in a human way that flags what they do and any destructive actions
Meaningful exit codes - document them
Idempotence - LLMs can decide to call commands multiple times
Avoid commands that depend on cwd - make it put the path in
Avoid natural language arguments, make it use explicit arguments where possible
Give agent option to limit output
Use UNIX principles 'one thing well', 'stateless' with structured output
Dry run option

Go to branch 7-* of bash-and-agents-project, run up `claude` and then 'deploy the app to test if it looks healthy, and extract the deploy time'

NOTE that the agent can 'guess' commands (eg version number) so you have to be explicit in your AGENTS.md that it should be got from the user.
Scripts and tools can be predictable, but agents can be unpredictable!
