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
- The columns "not now", "maybe", and "done" exist by default. We commonly add a column "In progress".
- The colums mean this:
  - "Not now" means "we want to deal with this topic later but AI should ignore it until a HUMAN moves the card into another column"
  - "Done" means "archived and not urgent any more". AI should ignore cards in that column altogether.
  - "In Progress" means this is what an AI or a human (depending on who is assigned) is currently be doing/prioritizing/working on.
  - "Maybe" is a drafting and discussion area for humans and AIs to sync, before things go to "in progress".

## Do's and Don'ts

- NEVER edit the main content of a card that someone else created. You may add comments (`comment create`) and check-off steps (`step update ID`) etc. on foreign cards, but NEVER modify the card body.
- You MUST use the fizzy CLI and its skill to use Fizzy (NEVER use raw HTTP to access the API).
- NEVER assign more than one person to a card (it muddles responsibilities). Either none, or one.
- NEVER comment to inform about card status updates ("I assigned you", "Moving to 'in progress'", "Reopened", "Got it, I will work on this"), that is obvious from the card state.
- Comments SHOULD be used to either ask clarifying questions, or to post a full workable plan, or to post *major* achieved milestones.
- Comments MUST be *meaningful* and adhere to the `../conversation-tldr/SKILL.md` style.
- Use comments sparingly, try to re-use comments whenever possible (e.g. when your's is the last comment), to not add more and more comments.

## Responsibilities

Follow this procedure (unless specifically asked otherwise):

- Ignore "Not now" and "Done" cards.
- Go through each card "In Progress" that you are assigned to. For each car, ask yourself:
  - Are there open questions here by a human? Answer them in a meaningful comment (TLDR-style, re-use if the latest comment is by you).
  - Am I blocked? Report the blocker, unassign yourself and assign the human.
  - What is the status of my work on this? Comment with a link to an actionable PR to be reviewed and assign the human (to the fizzy card, not the PR).
- Once there are no "In Progress" cards left that you have to act upon, go through *every* card in "Maybe". For each card, ask yourself:
  - Is a human assigned to the card? Then ignore it.
  - Am I assigned? Then check if it's the right column and move it to the correct column if needed.
  - Are there open questions?
    - What that means is, are there ANY open questions in the card body, steps, or comments? (The card body might be updated with more/new questions!)
    - Then use the `../procedure-research-skill/SKILL.md` to deliver actionable answers
  - Am I idle and could work on this? Then move it to "In Progress"

### Priority Overrides

- Go through your notifications first. It may update your knowledge or prompt you to work on a forgotten card.
- A card marked as "golden" is considered top priority, no matter in which column. Do nothing else unless the human is happy with how you dealt with the golden card. Do not remove the gold yourself, only a human may change the goldness of a card.
