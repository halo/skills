---
name: procedure-ensons-subagent
description: Run or orchestrate read-only ensons work on a disposable Hetzner host through Bedrock Ansible.
---

# Disposable ensons workflow

Use this skill when a request involves testing or inspecting `ensons/ensons` on a temporary Ubuntu host, or asks to use a subagent for that work.

## Non-negotiable boundary

The ensons repository is read-only. Do not run `git commit`, `git push`, `git merge`, `git rebase`, `git worktree`, or any equivalent mutation in that repository. Do not ask a subagent to create a PR from it. A disposable clone is for inspection and testing only.

## Provisioning

Use the executable helper in `scripts/run` for a repeatable run. It uses the official `/home/devops/.local/bin/hcloud` shim, which obtains the Hetzner token through the configured 1Password environment. The default host uses Ubuntu 24.04, `cpx32`, `fsn1`, firewall `11690281`, and both SSH keys `agent-computer` and `orange.halo.hetzner.de`.

The helper always deletes the server in its cleanup path. If it stops unexpectedly, inspect Hetzner immediately and delete any server it created before doing other work.

## Remote changes and tests

All changes on the temporary host go through the Bedrock Ansible role `ansible/roles/ensons-test` and `ansible/playbooks/agents.yml --tags ensons_test`. Do not install packages, copy secrets, clone the repository, or edit files with ad hoc SSH commands.

The role clones the live GitHub repository over SSH into `/data/ensons`, copies the controller-provided `.env.test` through Ansible, installs the pinned Ruby, Node, pnpm, and Playwright dependencies on the host, and starts only the repository's Docker Compose dependencies. Chromium and all tests run directly on Ubuntu.

Treat the Minitest summary as authoritative. The repository's `bin/tests` wrapper can return zero despite failures, so a successful Ansible exit is not a passing test unless both lanes report zero failures and zero errors. Report the exact summaries and representative root causes.

## Subagents

Subagents may inspect the disposable checkout and run tests. Do not permit them to commit, rebase, merge, push, create worktrees, or open a PR while the read-only rule is in force. Keep their work bounded to the requested investigation and retain the real test output.

The workflow is not complete merely because the host was provisioned or a command exited zero; it is complete only when the requested evidence was collected and the disposable server was removed.
