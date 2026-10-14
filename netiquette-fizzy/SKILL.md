---
name: netiquette-fizzy
description: Interact with Fizzy via the Fizzy CLI.
---

# Fizzy

Read `./CLI.md` to learn about Fizzy CLI commands and their data structures. You MUST use the fizzy CLI and its skill to use Fizzy (NEVER use raw HTTP to access the API).

## Boards

One board corresponds to one project/domain/namespace/person, examples:

- An IT project to get SIP telephony working
- A feature request tracker for the ERP system, where employees can add and tack ideas
- A notebook-like memory-space around ONE particular human where the AI assistant can dump reminders and spontaneous short-term and long-term ideas.
- A customer's construction site to track all tasks and TODOs related to that site (not per customer legal name, but always per address/site).

## Columns (of a board)

We only use the default columns ("not now", "maybe", and "done"):

- "Not now" means "we want to deal with this topic later but AI should ignore it until a HUMAN moves the card into another column"
- "Done" means "archived and not urgent any more". AI should ignore cards in that column altogether.
- "Maybe" is a drafting, discussion, and "in progress" area for humans and AIs

## Cards (in a column)

- NEVER edit the main content of a card that someone else created. You may add comments (`comment create`) and check-off steps (`step update ID`) etc. on foreign cards, but NEVER modify the card body.
- NEVER assign more than one person to a card (it muddles responsibilities). Either none, or one.
- Fizzy cards are primarily the assistant's working memory and decision log, not a list of deliverables to hand back to the human.
- "Track this in Fizzy" means preserve the thread in the most relevant existing card. It does **not** mean create a new card by default.
- Before creating a card, search the current board and reuse the closest existing card. Create a new card only when the human explicitly asks for one or when no existing card can hold a genuinely separate, durable thread.
- When several assistant-created cards describe the same thread, consolidate into the oldest relevant card and delete the duplicates. Never delete a card created by the human without explicit permission.

## Assignment rules

- Work on one actionable card at a time. Before acting, assign yourself.
- Continue working while an actionable next step remains; do not stop merely to announce that step.
- When the acceptance condition is met and the evidence is recorded, assign the human to invite feedback (do not consider the card "done" unless a human says so).

## Working on cards

When the user says "check fizzy", read the `../procedure-research/SKILL.md` tool and follow this procedure:

- Ignore "Not now" and "Done" cards.
- Go through each card "Maybe" that you are assigned to. For each card, ask yourself:
  - Am I blocked? Report the blocker, unassign yourself and assign the human.
  - Are there *any** open questions in the card body, steps, or comments (the card body might have been updated with more/new questions), then comment with answers.
  - Are there no blockers and I could continue to work on it? Work on it (no comment needed for this, assignment indicates you're busy)

### Priority Overrides

- If the user explicitly tells you to deviate from the above stated procedure, then follow the user's wishes.
- Go through your notifications first. They may update your knowledge or prompt you to work on a forgotten card, but treat them as context: the current board and card state are authoritative.
- A card marked as "golden" is considered top priority, no matter in which column. Do nothing else unless the human is happy with how you dealt with the golden card. Do not remove the gold yourself, only a human may change the goldness of a card.

## Comments (on a card)

### When to write a Comment

Write a comment only when it does one of these jobs:

- Answers a human direct question.
- Records a blocker.
- Records a meaningful plan or milestone that absolutely needs to remain in the card. "Meaningful" means, actionable for the human.
- NEVER comment to inform about card status updates ("I assigned you", "Moving to 'done'", "Reopened", "Got it, I will work on this", "Closing"), that is obvious from the card state.
- Use comments sparingly. When the latest comment(s) belong to you, harmonize them into a single up-to-date comment, rather than adding more comments.

### How to write a Comment

Read [EXAMPLES.md](./EXAMPLES.md) and `../conversation-tldr/SKILL.md`  whenever you write or review a Fizzy comment.

#### Syntax

- Keep the comment short enough to scan, normally no more than three paragraphs or about 80 words.
- Use paragraphs with headlines (`###` not `##`) and bullet points for readability (use markdown).

#### Relevant information (one per paragraph)

- The major outcome or genuine blocker in one sentence
- Description of the user-visible behavior or problem
- Actionable step for the user to take, if one remains

#### Irrelevant information

- Commands, paths, identifiers, timings, and assertion counts are only relevant when they are needed to reproduce a problem or make a decision.
- Never claim behavior that the cited observation or test does not establish.
- Saying "unit tests are green but I didn't run system tests" is not helpful, do you have a good reason to not try to run the system tests?
