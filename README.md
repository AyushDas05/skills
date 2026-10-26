# skills

Skills for Claude that I've written and use myself. Each one is a folder with a `SKILL.md` you can drop into Claude Code or upload to claude.ai.

## Skills

| Skill | What it does |
|---|---|
| [ask-clarifying-questions](docs/ask-clarifying-questions.md) | Before starting work, asks only the questions that matter, then sends a summary with its assumptions and concerns (ranked High / Medium / Negligible) and waits for your yes. |

## Install a skill

Download or clone this repo. Each skill is the folder with its name (for example [`ask-clarifying-questions/`](ask-clarifying-questions/)).

**Claude Code** (CLI, VS Code, JetBrains): copy the skill's folder into your skills directory.

```bash
git clone https://github.com/AyushDas05/skills.git

# For all your projects
cp -r skills/ask-clarifying-questions ~/.claude/skills/

# Or for one project only (run from the project root)
cp -r path/to/skills/ask-clarifying-questions .claude/skills/
```

**claude.ai and the Claude desktop app:** ZIP the skill's folder itself (so the ZIP holds `ask-clarifying-questions/SKILL.md`), upload it in your Skills settings and switch it on. See Anthropic's [guide to using skills](https://support.claude.com/en/articles/12512198).

Each skill's page in [`docs/`](docs/) explains how it works and how to customise it.

## License

[MIT](LICENSE)
