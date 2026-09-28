---
name: netiquette-git
description: (Not implemented yet)
---

# Levels of permissions

## History hygiene

- When a local branch and its remote have diverged, prefer rebasing replayable local commits onto the current remote tip instead of creating a synchronization merge commit. Keep `main` linear unless a merge is intentional and meaningful.
- Never rewrite published remote history by default. If the user explicitly asks to repair an accidental merge, verify the remote tip first, preserve unrelated working-tree changes, rebase the intended commits, and use `git push --force-with-lease` rather than an unconditional force push. Report the rewritten commit IDs.
- Use a merge commit only when both lines of history are intentionally retained or the project’s documented policy requires it.

## Default (most restrictive)

- By default, never use git stash, checkout, worktree, commit, or push, etc. (destructive operations).
- One reason: if you're in the `Docker Sandbox` environment, that means you share a local repo directory with another user (on the laptop host).

## Spike Programmer

- If asked to fix or implement something, and the git worktree is somewhat clean, modify files but don't do git operations on them.
- You can give quick feedback to the user about whether it's going into the right direction, the user himself will do all git operations manually.

## Pull Request Programmer

- If asked to "create a PR", you're a decision-preparing agent.
- If the git tree is clean, you may work with branches and worktrees (even in `Docker Sandbox`)
- You may make modifications of the files the local repository, but don't push to the  main branch on github.
- If you push, only push a feature branch and open a PR for a human to review/merge manually.
- Expect a Github review by a human.

## Autonomous Programmer (least restrictive)

- If you are asked to work on a feature in repositories fully autonomous (e.g. that are on a server and not in critical production use, or explicitly told to work autonomously), you are in this least restrictive permission level.
- It suggests that you "own" the repositories (at least for the time being), you may use branches, worktrees and push freely to Github, even to the `main` branch. Indeed, you should push it pro-actively.
- Do not override GitHub history by default; an explicit user request to repair a known history mistake is the exception, and still requires `--force-with-lease` plus a verified remote tip.
