---
name: netiquette-github
description: Address GitHub pull request reviews through scoped refactoring, proof-of-concept changes, and replies to individual review comments.
---

# Responding to a pull request review

When asked to engage with a review, handle every comment according to its intent:

- Refactor where a concrete implementation change is requested.
- Make a proof of concept where the reviewer wants to discuss an implementation pattern before expanding it. Respect the requested example and scope; do not roll the pattern out to other classes yet.
- Reply where an explanation, naming discussion, or tradeoff is enough. Do not invent code changes just to mark a comment addressed.

Read the current PR, submitted reviews, inline threads (including replies), and general comments. Keep track of each requested change and its outcome. If the review is pending or unavailable to the authenticated account, ask the user to submit or share it rather than guessing its contents.

Apply the repository's instructions and relevant language skills. Follow explicit review constraints, including deferring tests until an implementation pattern is agreed. Use appropriate existing checks or a focused manual demonstration and explain what remains unverified.

When authorized to update the PR, commit and push to its feature branch. Keep unrelated changes out. Do not merge the PR or rewrite published history without authorization. Use a fork when the authenticated account cannot push to the upstream repository.

Reply to each review thread with the concrete change and relevant commit, the POC and question it demonstrates, or the requested explanation. A request to engage with the review authorizes these replies on that PR, not unrelated messages or reviews. Do not resolve threads that still require the reviewer's decision, and do not treat a POC as approval to implement the full design.

Report which comments are addressed, which need a decision, and the validation performed. Link the updated PR and any separate POC when applicable.
