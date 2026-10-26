---
name: ask-clarifying-questions
description: >
  Before starting work, asks only the questions that matter, then summarises assumptions and ranked concerns and waits for a yes. Use on /ask-clarifying-questions, "ask me first" or "don't assume".
---

# Ask Clarifying Questions

The user adds this to a prompt when they suspect they haven't given enough context: the reasoning behind a request, a constraint they forgot, a detail they assumed was obvious. Your job is to find those gaps and ask about them. Do not fill them with guesses, and do not start the work.

The reason this exists: a confident answer built on a wrong assumption costs the user a redo. A few sharp questions up front cost them less.

## 1. Pin down the original ask

Identify what the user is actually asking for, in this order of preference:

1. The text before the command in the same message.
2. The command's arguments, if any.
3. If the message is only the command, their most recent substantive request in the conversation.

If there is no request anywhere (the message is only the command and nothing came before), your one question is "What would you like done?"

Strip the `/ask-clarifying-questions` command itself from the ask. Write the original ask down for yourself in one or two sentences, with their wording intact. If the prompt holds several separate asks, list each one. You will restate the ask on every round, because long question chains drift and the thing they first asked for is what has to survive.

If the user changes the ask partway through ("actually, make it X instead"), re-pin it and say so in the next anchor line: *Original ask (updated): "…"*.

## 2. Find the gaps before asking anything

Gather context first, so you only ask what only the user can answer:

- Re-read the conversation so far.
- If a project is in play, read what the user's own docs already say, plus a few targeted searches if the ask points at specific code. Being asked for something that is already written down is the fastest way to lose the user's trust in this process. Don't go on an expedition. Where the docs live depends on where you're running:
  - **In a code repo:** README, `CLAUDE.md` / `AGENTS.md`, PRD or spec, notes, a TODO list.
  - **In a claude.ai Project:** the Project's instructions and its uploaded knowledge files. Treat them exactly as you would a repo's docs, including checking for recorded decisions that conflict with the ask.
  - **In any chat with memory:** what memory says about the user and this topic.
- If none of those exist (no PRD, no files, a plain chat), that's fine: there's simply less written down, so more gaps become questions rather than assumptions. Don't ask the user to produce a PRD or upload files unless the work genuinely can't proceed without them.
- If the ask isn't about a project (a piece of writing, a decision, a plan, a trip), skip the file reading and work from the conversation. Don't ask "which project?" here.
- Ask "which project?" only when the ask is clearly about a specific project and you can't tell which one (several candidates, or a working directory that doesn't match). Don't go reading projects to guess; make it the first question, and read that project's docs once they answer.
- Anything you can answer from those sources, from a quick check you can run yourself (reading a file, running a command), or from a sensible default, is **not** a question. Note it as an assumption instead. The exception: if a wrong guess would mean redoing meaningful work, ask, even when you have a best guess. That is when "(Recommended)" earns its place.
- **Conflicts are always questions.** If the ask contradicts a recorded decision (a product decision, something the user previously asked you to change) or asks for something that already exists, don't settle it quietly. Ask, naming the source (file and date) and what you found. This is often the most valuable question in the whole round.

Then list what remains genuinely open. Good candidates:

- **Why**: the goal or outcome behind the request (the gap people most often leave).
- **Scope**: what's in, what's explicitly out, what's now versus later.
- **Success**: how they'll judge it done; what they'll check.
- **Constraints**: stack, cost, timeline, audience, things that are fixed.
- **Fork in the road**: two reasonable interpretations that would lead to meaningfully different work.
- **Inputs**: a reference, file, link, or data they have in mind but didn't attach.

Rank by how much the answer would change what you do. Ask about the top ones. A question whose answer wouldn't change the work isn't worth their time.

**Question or concern?** If the answer would change *what you build*, it's a question. If it only changes *the risk* of what you build, it's a concern. Start a private **concerns list** now and keep adding to it through every round: risks, doubts about the approach, things that could go wrong even if every question gets a good answer. It feeds the summary (section 4).

## 3. Ask in rounds

**Each round: at most 4 questions, using the `AskUserQuestion` tool.** Fewer is better; if two or three questions cover it, ask two or three.

If the `AskUserQuestion` tool isn't available in this environment, ask the same round as a plain numbered list with lettered options, and let the user reply like "1a, 2c, 3: skip".

Shape each question so it's quick to answer:
- Give 2–4 concrete options. Put your best guess first and mark it "(Recommended)" only when you have a real basis for it; if you are honestly guessing, leave the tag off and say so in the question. The user can always type something else.
- Make each option's description say what it would mean for the work.
- Keep question text short and specific. No two questions bundled into one.
- One round usually covers the biggest forks. Let the answers inform the next round rather than asking everything blind. If a question would be pointless under one of the likely answers to another question, either hold it for the next round or add "skip if you picked X".
- If the "why" behind the request is missing, spend one slot on it ("what prompted this?"), unless the reason is obvious from the ask (a typo fix needs no why). The answer should then shape the plan, and the summary should show how.

Before the questions, in your message text (not inside the tool call), put one line restating the original ask, like: *Original ask: "…"  — Round 2 of 3.* This is how the thread stays anchored. If you found something that changes how the questions read (an existing feature, a conflicting decision, a gap in the data), add a **What I found** list right after that line, numbered, one finding per item, each starting with a short bold topic:

> **What I found**
> 1. **Topic:** the context, with the source (file and date) where there is one.
> 2. **Topic:** the context.

Length isn't capped, but each item should earn its place: include only findings that change how the questions read, and keep each to a line or two. In round 1, add one more line: *Say "skip" or "just go" at any point to jump to the summary.* Questions must serve the ask; if a question could be asked of any request, it's too generic.

**Never reopen an answered question.** Once the user has answered something, it's settled for this cycle. If you'd push back on their answer, that goes in the summary as a High concern, not into another question. If they leave a question unanswered, treat it as a default (an assumption), unless it's material, in which case carry it into the next round once.

**Cap: 3 rounds, then the summary.** After each round, check the answers against the open list. If nothing material is left, go to the summary early. Don't spend rounds for the sake of it.

**Before every round after the first, ask yourself: could I go ahead now on sensible defaults?** If yes, go to the summary instead. Any remaining question where you'd mark an option "(Recommended)" and a wrong guess would be cheap to fix is an assumption, not a question. Only run another round for gaps where a wrong guess would mean redoing meaningful work. This matters most in plain chats, where there's nothing to look up and every gap is tempting to ask about. After round 3, go to the summary even if things are still open: list each open item there as an assumption (with the default you'd use) or, if a wrong default would be costly, as a High concern.

**Early exit.** If the user says "skip", "enough questions", "just go", "start", or anything like it while you're still asking, stop asking and go straight to the summary. **This never means start the work.** Even "just go" gets a summary first, and the work waits for a go-ahead on that summary.

In that summary, say plainly that questions were cut short, and list each skipped question under Assumptions with the default you chose ("You skipped this, so I'll go with X"). Don't turn skipped questions back into concerns that demand answers: the user just said they don't want more questions. Raise one as a High concern only if the default risks real rework, and then phrase it as what you'll do unless they say otherwise. Don't argue for another round.

## 4. Summary, then wait

When the gaps are closed (or the rounds or the user's patience have run out), do not start the work. Send a summary:

- **Your original ask** (their words, lightly tidied).
- **What I now understand**: their answers folded in, as a few bullets, including how the "why" shapes the plan.
- **Assumptions I'm making**: anything you chose a default for rather than asking, including skipped questions.
- **Concerns**: see below.
- **What I'll do first**, in a line or two.
- **Worth saving**: only if some answers will last beyond this task; see below.

Leave out any heading that would be empty. **Match the summary to the size of the task.** For something small (a typo, a one-line fix), the whole summary is two to four lines: what you'll change, any assumption, and the go-ahead question.

### Concerns

An assumption is a gap you filled with a default. A concern is different: a risk or doubt you still hold about the plan, even with every question answered. Surfacing it gives the user the chance to add context that dissolves it, or to change course before any work is done. Group them by severity:

- **High**: could cause rework, break something, waste money, or miss the user's actual goal. Also any answer they gave that you'd push back on.
- **Medium**: worth knowing, but with a clear way to handle it.
- **Negligible**: minor, listed for transparency.

Write each concern as one line: what the concern is, why it matters, and what you'll do about it unless the user says otherwise (or what context would dissolve it). For example: *"High: the export runs on every page load; at your stated traffic that's ~40k API calls a day, past the free tier. Unless you say otherwise, I'll cache it and refresh once a day."*

Be honest, not exhaustive:
- A concern must be about this task. General facts about the environment ("this folder isn't under version control") don't belong unless they make this task risky.
- Don't invent concerns to fill a tier, and keep Negligible to a few items at most. If you have no concerns at all, say "Concerns: none" in one line (on a small task, just leave the heading out).
- Aim for about five concerns in total at most. If you have more, merge related ones or drop the weakest; a long list hides the ones that matter.
- Don't list something here that you're already offering under "Worth saving".
- Never bury a High concern inside a Medium one to keep the summary tidy.

### Worth saving

Some answers apply beyond this task: a standing preference ("always mobile-first"), a constraint ("no paid APIs"), a reversed product decision. If any of the user's answers are like that, list them in one line each and offer to save them. This is what stops the same question coming up next time. Only offer what you can actually do where you're running:

- **You can edit files** (a code repo): offer to write them where the project keeps such things (`CLAUDE.md`, the PRD's decision log, a notes file), naming the exact file.
- **You can't edit files** (claude.ai chats and Projects): offer to save them to memory if memory is available. Otherwise give the user a ready-to-paste line and say where to paste it: their Project instructions if they're in a Project, otherwise their custom instructions (personal preferences) or their own notes. Never offer to update a file you can't write to.

Only offer for answers that really will hold beyond this task, and never save anything without a yes.

### Ask for the go-ahead

End with two lines:

1. Ask whether to go ahead. If there are High concerns, ask the user to read those before they answer. Saying yes accepts your assumptions and what you said you'd do about each concern.
2. Tell them that if anything is still unclear, especially a High concern, they can say "more questions" and you'll run another round. Leave this line out for a small task with no concerns, where it's just padding.

Only an explicit yes in reply to this summary counts as the go-ahead: "yes", "go ahead", "approved", "just go", or similar. A go-ahead given earlier in the conversation doesn't count, and neither does a "just go" said while you were still asking questions.

## 5. After the summary

- **They approve**: do the work.
- **They ask for changes**: update the summary and ask again. Don't open new questions unless the change creates a real new gap.
- **They ask for more questions, or their reply leaves a High concern unclear**: start a fresh cycle of up to 3 rounds. Keep the original ask pinned, carry forward everything they've already answered so you never re-ask it, and end with a new summary.
- **Nobody can answer** (you're running in the background, on a schedule, or as a subagent whose output no person will read before it's used): skip the question rounds, post the summary with your defaults as assumptions, and stop. Don't do the work. Open the summary with one line saying it ran unattended and nothing was changed, and end it by saying a person can approve the plan later. Leave out the "more questions" line, since nobody is there to ask.

## Tone

Plain English, matched to the user's level and vocabulary. Give a brief "why" where a question might seem odd. Avoid jargon. If the prompt is already thorough and you find nothing material to ask, say so in a line, give the summary, and ask for the go-ahead. Don't invent questions to justify the command.

## Examples

### A small task

Prompt: *"Fix the typo 'Favourties' in the header /ask-clarifying-questions"*

> Nothing to ask. I'll change "My Favourties" to "My Favourites" in `src/App.tsx` (line 25), using British spelling to match your docs. Go ahead?

### A bigger task

Prompt: *"Add a dark mode to my app /ask-clarifying-questions"*

Claude reads the project first. `CLAUDE.md` says colours live in `tokens.css`. `PRD.md` says "Light theme only for v1" (decided 2026-03-02). A search finds hard-coded hex colours in 14 components. The logo is a PNG with a white background; an SVG version also exists in `assets/`.

**Round 1.** Claude writes this as message text:

> *Original ask: "Add a dark mode to my app." — Round 1 of 3.*
>
> **What I found**
> 1. **Recorded decision:** your PRD locks v1 to a light theme only (PRD.md, 2026-03-02), so this reverses it.
> 2. **Existing setup:** colours already live in `tokens.css`, which makes adding a dark set straightforward.
>
> Say "skip" or "just go" at any point to jump to the summary.

Then it calls `AskUserQuestion` with three questions:

1. **Override the light-only decision for v1?** No recommendation: this is your product call.
   - *Yes, ship it in v1*: reverses the PRD decision; I'd offer to update the PRD.
   - *Build it behind a flag, off by default*: built now, but nobody sees it until you switch it on.
   - *Plan it for v2 only*: nothing is built; I'd add it to the v2 list.
2. **What prompted this?** I'm guessing, so no recommendation.
   - *User requests*: I'd focus on the screens people use most.
   - *Eye strain or accessibility*: I'd put readability and contrast ahead of looks.
   - *Brand refresh*: I'd treat it as a design job with new colour choices.
3. **Which parts go dark?**
   - *App screens only; emails and PDF exports stay light* (Recommended: emails follow the reader's mail app, so dark versions are a separate job)
   - *Everything, including emails and PDFs*: much larger, and emails need testing in each mail app.

The user picks: *Yes, ship it in v1* · Other: "support tickets about eye strain at night" · *App screens only*.

How the theme gets chosen (system setting, a toggle, or both) wasn't asked. There's a common default and it's cheap to change later, so it goes in as an assumption. Nothing material is left open, so Claude skips rounds 2 and 3.

**Summary**:

> **Your original ask:** Add a dark mode to the app.
>
> **What I now understand:**
> - Dark mode ships in v1 on the app screens. Emails and PDF exports stay light.
> - It's driven by support tickets about eye strain at night, so I'll put readability first: text contrast that meets accessibility guidelines, dark grey rather than pure black, and no bright white panels.
>
> **Assumptions:** the app follows the device's light/dark setting, with a toggle in settings that remembers the choice in the browser. Charts and photos stay as they are unless they become hard to read.
>
> **Concerns:**
> - High: 14 components use hard-coded colours instead of `tokens.css`, so they'd stay light in dark mode. Unless you say otherwise, I'll move them into `tokens.css` first. That's an extra step before anything visibly changes.
> - Medium: the logo PNG has a white background and would show as a white box. I'll use the SVG version in `assets/` instead.
> - Negligible: some browsers flash the light theme for a moment on first load. I'll add the standard small script that prevents it.
>
> **What I'll do first:** move those 14 components' colours into `tokens.css`, then add the dark values.
>
> **Worth saving:** "v1 includes dark mode, on app screens only." Want me to update the decision in `PRD.md` so it isn't read as light-only later?
>
> Shall I go ahead? Please read the High concern first; saying yes accepts the plan for it.
> If anything's still unclear, say "more questions" and I'll run another round.
