---
name: netiquette-fizzy
description: Interact with Fizzy via the Fizzy CLI.
---

# Fizzy

Read `./CLI-SKILL.md` first, to learn about Fizzy CLI and its data structures (the original is at https://github.com/basecamp/fizzy-cli/blob/master/skills/fizzy/SKILL.md)

## Overview

- One board corresponds to one project/domain/namespace/person, examples:
  - An IT project to get SIP telephony working
  - A feature request tracker for the ERP system, where employees can add and tack ideas
  - A notebook-like memory-space around ONE particular human where the AI assistant can dump reminders and spontaneous short-term and long-term ideas.
  - A customer construction site to track all tasks and TODOs related to that site (not per customer legal name, but always per address/site).
- Every board has exactly the three default columns: "Not Now", "Maybe?", and "Done". Do not create or use an "In Progress" column.
- The columns and assignments mean this:
  - "Not Now" means "we want to deal with this topic later". Ignore it, even if somebody is assigned, until a human moves it elsewhere.
  - "Maybe?" is the only active working and discussion area. An assigned card has an active owner; an unassigned card is available or waiting for a decision.
  - "Done" means archived and no longer urgent. Ignore it. If follow-up work is needed, reopen or move the card to "Maybe?" and assign the person doing it.
  - When starting work, assign yourself to the card. When stopping work, unassign yourself. There must be at most one assignee.

## Do's and Don'ts

- NEVER edit the main content of a card that someone else created. You may add comments (`comment create`) and check-off steps (`step update ID`) etc. on foreign cards, but NEVER modify the card body.
- You MUST use the fizzy CLI and its skill to use Fizzy (NEVER use raw HTTP to access the API).
- NEVER assign more than one person to a card (it muddles responsibilities). Either none, or one.
- NEVER comment to inform about card status updates ("I assigned you", "Moving to 'in progress'", "Reopened", "Got it, I will work on this"), that is obvious from the card state.
- Comments SHOULD be used to either answer a human question, ask a precise clarifying question, record a workable plan, or record a *major* achieved milestone.
- Comments MUST be *meaningful* and adhere to the `../conversation-tldr/SKILL.md` style.
- Use comments sparingly, try to re-use comments whenever possible (e.g. when your's is the last comment), to not add more and more comments.

### Comment decision

Write a comment only when it does one of these jobs:

- Answers a human question.
- Records a plan that needs to remain in the card.
- Records a meaningful milestone or blocker.
- Otherwise, do not comment; assignment, column movement, and closure are already visible in the card state.

For a milestone or blocker comment:

1. Lead with the outcome or blocker in one sentence.
2. Use Markdown headings for more than one section. Never use an `#` heading; `##` is the largest permitted heading, and `###` is preferred for ordinary sections.
3. Add no more than two or three bullets describing the user-visible behavior or decision.
4. Continue with the next actionable step when one remains; mention an unresolved item only when it is a genuine blocker or requires a human decision.
5. Use `---` followed by `### Your turn` only when a precise answer or decision is needed from the human.

Keep the comment short enough to scan, normally no more than three short sections or about 80 words. This is a length target, not a line-width rule. Summarize verification at the level that changes confidence: for example, say that automated coverage is green or that live verification is the next action. Include commands, paths, identifiers, timings, and assertion counts only when they are needed to reproduce a problem or make a decision. Summarize empty or no-op inspection results in one sentence rather than narrating the inventory. Never claim behavior that the cited observation or test does not establish. Do not write “Completed” or “Done” as a comment; the card state records that.

Read [EXAMPLES.md](./EXAMPLES.md) whenever you write or review a Fizzy comment.

### Cards are assistant memory

- Fizzy cards are primarily the assistant's working memory and decision log, not a list of deliverables to hand back to the human.
- "Track this in Fizzy" means preserve the thread in the most relevant existing card. It does **not** mean create a new card by default.
- Before creating a card, search the current board and reuse the closest existing card. Create a new card only when the human explicitly asks for one or when no existing card can hold a genuinely separate, durable thread.
- When several assistant-created cards describe the same thread, consolidate into the oldest relevant card and delete the duplicates. Never delete a card created by the human without explicit permission.

## Responsibilities

### One-card work loop

- Work on one actionable card at a time. Before acting, assign yourself and leave the card in "Maybe?".
- Continue working while an actionable next step remains; do not stop merely to announce that step.
- When you stop working, unassign yourself. If nobody is actively working on the card, it belongs in "Maybe?".
- Close the card only after its acceptance condition is met and the evidence is recorded.

When the user says "check fizzy", follow this procedure:

- Ignore "Not now" and "Done" cards.
- Go through each "Maybe?" card that you are assigned to. For each card, ask yourself:
  - Are there open questions here by a human? Answer them in a meaningful comment (TLDR-style, re-use if the latest comment is by you).
  - Am I blocked? Report the blocker, unassign yourself and assign the human.
  - What is the status of my work on this? Comment with a link to an actionable PR to be reviewed and assign the human (to the fizzy card, not the PR).
- Once there are no assigned "Maybe?" cards left that you have to act upon, go through *every* unassigned card in "Maybe?". For each card, ask yourself:
  - Is a human assigned to the card? Then ignore it.
  - Am I assigned? Then check if it's the right column and move it to the correct column if needed.
  - Are there open questions?
    - What that means is, are there ANY open questions in the card body, steps, or comments? (The card body might be updated with more/new questions!)
    - Then use the `../procedure-research/SKILL.md` to deliver actionable answers
    - Am I idle and could work on this? Then assign yourself and work on it in "Maybe?".

### Priority Overrides

- If the user explicitly tells you to deviate from the above stated procedure, then follow the user's wishes.
- Go through your notifications first. They may update your knowledge or prompt you to work on a forgotten card, but treat them as context: the current board and card state are authoritative.
- A card marked as "golden" is considered top priority, no matter in which column. Do nothing else unless the human is happy with how you dealt with the golden card. Do not remove the gold yourself, only a human may change the goldness of a card.
