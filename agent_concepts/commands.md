## Commands

https://code.claude.com/docs/en/commands

Things beginning with /

Type / to see commands
    eg a markdown file with instructions, eg 'Review my pull request'
    What's the difference between a command and skill? A command is explicitly called by the user. A skill is dynamically chosen by the LLM and then invoked into the context. Confusingly, a skill can be invoked as a command as well.
    Commands have now been merged into skills: .claude/commands/x.md and .claude/skills/x/SKILL.md both give you /x. The commands/ form still works; skills add supporting files and invocation controls.

#### Create two commands

cat .claude/commands/ok.md
watch for the removal of a file 'asd' in this folder, and when it is removed, output 'ok!'

cat .claude/commands/argh.md
watch for the creation of a file 'asd' in this folder, and when it is created, output 'argh!'

Then start claude:

```
/ok                  # to get ok monitor running
/argh                # to get argh monitor running
```

Create and remove the files

```
touch asd
rm asd
```

Try running them again:

```
touch asd ## Note that the monitor only runs once.
```


### Cleanup

`rm -f .claude/commands/ok.md .claude/commands/argh.md`

(or `~/.claude/commands/...` if you created them at user level)

