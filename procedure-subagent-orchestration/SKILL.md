---
name: procedure-subagent-orchestration
description: Run or coordinate repository work on disposable VPS hosts through Bedrock Ansible.
---

# Orchestra disposable workbench

Use this skill when Orchestra needs subagents to test or inspect a DevOps/Ensons-related repository on a temporary Ubuntu host. The current helper implements the Ensons test workflow; related repositories may add their own Bedrock role and test command under the same procedure.

## Non-negotiable boundary

The target repository is read-only during this workflow. Do not run `git commit`, `git push`, `git merge`, `git rebase`, `git worktree`, or any equivalent mutation in it. Do not ask a subagent to create a PR from it. A disposable clone is for inspection and testing only.

## Provisioning

Use the executable helper in `scripts/run` for a repeatable Ensons run. It uses the official `/home/devops/.local/bin/hcloud` shim, which obtains the Hetzner token through the configured 1Password environment. The default host uses Ubuntu 24.04, `cx23`, `fsn1`, firewall `11690281`, and both SSH keys `agent-computer` and `orange.halo.hetzner.de`.

Hetzner charges a minimum of one hour for a server. Never delete a newly created server in the cleanup path before that hour has elapsed. Keep a failed host available for diagnosis and retry; print its ID and address so the operator can use it. Delete it only after the first billable hour, or when the operator explicitly accepts the wasted minimum charge.

Use the cheapest available x86 server in Falkenstein that meets the job's actual requirements. For the ordinary Ensons test workflow, that means at least 2 vCPUs, 4 GB RAM, and 40 GB disk; use `cx23` as the normal starting point and move up only when the run proves that it needs more. Do not default to a larger server for convenience, and do not use an ARM type when the repository's images or browser dependencies require x86.

## Remote changes and tests

For the current Ensons workflow, all changes on the temporary host go through the Bedrock Ansible role `ansible/roles/ensons-test` and `ansible/playbooks/agents.yml --tags ensons_test`. Related repository workflows must likewise use their Bedrock role and playbook. Do not install packages, copy secrets, clone the repository, or edit files with ad hoc SSH commands.

The role clones the live GitHub repository over SSH into `/data/ensons`, copies the controller-provided `.env.test` through Ansible, installs the pinned Ruby, Node, pnpm, and Playwright dependencies on the host, and starts only the repository's Docker Compose dependencies. Chromium and all tests run directly on Ubuntu.

Treat the Minitest summary as authoritative. The repository's `bin/tests` wrapper can return zero despite failures, so a successful Ansible exit is not a passing test unless both lanes report zero failures and zero errors. Report the exact summaries and representative root causes.

## Subagents

Subagents may inspect the disposable checkout and run tests. Do not permit them to commit, rebase, merge, push, create worktrees, or open a PR while the read-only rule is in force. Keep their work bounded to the requested investigation and retain the real test output.

The workflow is not complete merely because the host was provisioned or a command exited zero; it is complete only when the requested evidence was collected. Server deletion is a separate cleanup action and must respect the first billable hour.
