---
name: netiquette-fizzy
description: Interact with Fizzy via the Fizzy CLI.
---

# Fizzy

Read `./CLI.md` to learn about Fizzy CLI commands and their data structures.

## Overview

- One board corresponds to one project/domain/namespace/person, examples:
  - An IT project to get SIP telephony working
  - A feature request tracker for the ERP system, where employees can add and tack ideas
  - A notebook-like memory-space around ONE particular human where the AI assistant can dump reminders and spontaneous short-term and long-term ideas.
  - A customer's construction site to track all tasks and TODOs related to that site (not per customer legal name, but always per address/site).
- The columns "not now", "maybe", and "done" exist by default and that's all we use.
- The colums mean this:
  - "Not now" means "we want to deal with this topic later but AI should ignore it until a HUMAN moves the card into another column"
  - "Done" means "archived and not urgent any more". AI should ignore cards in that column altogether.
  - "Maybe" is a drafting and discussion area for humans and AIs to sync and, once assigned to a human or an AI, the "in progress" indicator.

## Do's and Don'ts

- NEVER edit the main content of a card that someone else created. You may add comments (`comment create`) and check-off steps (`step update ID`) etc. on foreign cards, but NEVER modify the card body.
- You MUST use the fizzy CLI and its skill to use Fizzy (NEVER use raw HTTP to access the API).
- NEVER assign more than one person to a card (it muddles responsibilities). Either none, or one.
- NEVER comment to inform about card status updates ("I assigned you", "Moving to 'done'", "Reopened", "Got it, I will work on this"), that is obvious from the card state.
- Comments SHOULD be used to either ask clarifying questions, or to post a full workable plan, or to post *major* achieved milestones.
- Comments MUST be *meaningful* and adhere to the `../conversation-tldr/SKILL.md` style.
- Use comments sparingly, try to re-use comments whenever possible (e.g. when your's is the last comment), to not add more and more comments.

### Comment decision

Write a comment only when it does one of these jobs:

- Answers a human direct question.
- Records a plan that needs to remain in the card.
- Records a meaningful milestone or blocker.
- Otherwise, do not comment; assignment, column movement, and closure are already visible in the card state.

For a milestone or blocker comment:

1. Lead with the outcome or blocker in one sentence.
2. Add no more than two or three bullets describing the user-visible behavior or decision.
3. Continue with the next actionable step when one remains; mention an unresolved item only when it is a genuine blocker or requires a human decision.

- Keep the comment short enough to scan, normally no more than three paragraphs or about 80 words. This is a length target, not a line-width rule.
- Summarize verification at the level that changes confidence: for example, say that automated coverage is green or that live verification is the next action.
- Include commands, paths, identifiers, timings, and assertion counts only when they are needed to reproduce a problem or make a decision. Never claim behavior that the cited observation or test does not establish. Do not write “Completed” or “Done” as a comment; the card state records that.

Read [EXAMPLES.md](./EXAMPLES.md) whenever you write or review a Fizzy comment.

### Cards are assistant memory

- Fizzy cards are primarily the assistant's working memory and decision log, not a list of deliverables to hand back to the human.
- "Track this in Fizzy" means preserve the thread in the most relevant existing card. It does **not** mean create a new card by default.
- Before creating a card, search the current board and reuse the closest existing card. Create a new card only when the human explicitly asks for one or when no existing card can hold a genuinely separate, durable thread.
- When several assistant-created cards describe the same thread, consolidate into the oldest relevant card and delete the duplicates. Never delete a card created by the human without explicit permission.

## Responsibilities

### One-card work loop

- Work on one actionable card at a time. Before acting, assign yourself.
- Continue working while an actionable next step remains; do not stop merely to announce that step.
- When you stop working, unassign yourself. If nobody is actively working on the card, it belongs in "Maybe".
- Close the card only after its acceptance condition is met and the evidence is recorded (assign the human if unsure, to invite feedback).

When the user says "check fizzy", follow this procedure:

- Ignore "Not now" and "Done" cards.
- Go through each card "Maybe" that you are assigned to. For each card, ask yourself:
  - Are there open questions here by a human? Answer them in a meaningful comment (TLDR-style, re-use if the latest comment is by you).
  - Am I blocked? Report the blocker, unassign yourself and assign the human.
  - What is the status of my work on this? Comment with a link to an actionable PR to be reviewed and assign the human (to the fizzy card, not the PR).
  - Are there open questions?
    - What that means is, are there ANY open questions in the card body, steps, or comments? (The card body might be updated with more/new questions!)
    - Then use the `../procedure-research/SKILL.md` to deliver actionable answers
- Is a human assigned to the card? Then ignore it.

### Priority Overrides

- If the user explicitly tells you to deviate from the above stated procedure, then follow the user's wishes.
- Go through your notifications first. They may update your knowledge or prompt you to work on a forgotten card, but treat them as context: the current board and card state are authoritative.
- A card marked as "golden" is considered top priority, no matter in which column. Do nothing else unless the human is happy with how you dealt with the golden card. Do not remove the gold yourself, only a human may change the goldness of a card.
