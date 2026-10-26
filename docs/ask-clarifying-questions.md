# ask-clarifying-questions

A skill for Claude that asks the right questions before it starts work, instead of guessing.

Add `/ask-clarifying-questions` to the end of a prompt when you suspect you haven't given enough context. Claude reads what's already written down, asks only about the gaps it can't fill itself, then sends you a summary of its plan, its assumptions and its concerns. It does nothing until you say yes.

**Why:** a confident answer built on a wrong assumption costs you a redo. A few sharp questions up front cost less.

## What it does

1. **Pins your original ask** and restates it at the top of every round, so a long back-and-forth doesn't drift away from what you actually asked for.
2. **Reads before it asks.** It checks your project's README, `CLAUDE.md` / `AGENTS.md`, PRD or notes (or a claude.ai Project's instructions and files, or memory) and never asks something that's already answered there. If your request clashes with a decision you've written down, it says so, naming the file and date.
3. **Asks in short rounds:** at most 4 questions per round and 3 rounds. Every option says what it would mean for the work. It only marks an option "(Recommended)" when it has a real reason, and says so when it's guessing. Before each new round it checks whether it could go ahead on sensible defaults, and if so it stops asking.
4. **Sends a summary**, sized to the task:
   - what it now understands, including how your "why" shapes the plan
   - the assumptions it made instead of asking
   - its **concerns**, ranked **High / Medium / Negligible**, each with what it will do about it unless you say otherwise
   - what it will do first
   - answers worth saving for next time (to a file, to memory, or as a line for you to paste, depending on where it's running)
5. **Waits for an explicit yes** before doing anything.

## What you can say

| You say | What happens |
|---|---|
| "skip", "enough questions", "just go" (while it's asking) | It stops asking and goes straight to the summary. It never starts the work from this. |
| "yes", "go ahead", "approved", "just go" (after the summary) | It starts the work. |
| "more questions" (after the summary) | It runs another set of up to 3 rounds and never re-asks anything you've already answered. |
| Changes to the summary | It updates the summary and asks again. |

## Example

> **You:** Add a dark mode to my app /ask-clarifying-questions

> *Original ask: "Add a dark mode to my app." — Round 1 of 3.*
>
> **What I found**
> 1. **Recorded decision:** your PRD locks v1 to a light theme only (PRD.md, 2026-03-02), so this reverses it.
> 2. **Existing setup:** colours already live in `tokens.css`, which makes adding a dark set straightforward.
>
> Say "skip" or "just go" at any point to jump to the summary.
>
> 1. **Override the light-only decision for v1?** *Yes, ship it in v1* · *Build it behind a flag* · *Plan it for v2 only*
> 2. **What prompted this?** *User requests* · *Eye strain or accessibility* · *Brand refresh*
> 3. **Which parts go dark?** *App screens only (Recommended)* · *Everything, including emails and PDFs*

After you answer, the summary ends like this:

> **Concerns:**
> - High: 14 components use hard-coded colours instead of `tokens.css`, so they'd stay light in dark mode. Unless you say otherwise, I'll move them into `tokens.css` first.
> - Medium: the logo PNG has a white background and would show as a white box. I'll use the SVG version instead.
> - Negligible: some browsers flash the light theme for a moment on first load. I'll add the standard small script that prevents it.
>
> **Worth saving:** "v1 includes dark mode, on app screens only." Want me to update the decision in `PRD.md`?
>
> Shall I go ahead? Please read the High concern first; saying yes accepts the plan for it.

The full example, including a small-task version, is at the end of [`SKILL.md`](../ask-clarifying-questions/SKILL.md).

## Install

The skill is the [`ask-clarifying-questions/`](../ask-clarifying-questions/) folder in this repo. See the [install steps](../README.md#install-a-skill) on the repo's front page. In short, for Claude Code:

```bash
git clone https://github.com/AyushDas05/skills.git
cp -r skills/ask-clarifying-questions ~/.claude/skills/
```

Then type `/ask-clarifying-questions` at the end of any prompt.

On claude.ai, questions arrive as a numbered list you answer like `1a, 2c, 3: skip`. In Claude Code, they appear as clickable options.

## Customising

Everything lives in one file, [`SKILL.md`](../ask-clarifying-questions/SKILL.md), written in plain English. Common changes:

- **Rounds and questions per round:** section 3 ("Ask in rounds").
- **Concern levels and what counts as High:** "Concerns" in section 4.
- **Where lasting answers get saved:** "Worth saving" in section 4.

Keep the `description` in the file's header to 200 characters or fewer if you plan to upload it to claude.ai.

## License

[MIT](../LICENSE)
