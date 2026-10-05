---
name: procedure-subagent-orchestration
description: Run or coordinate repository work on disposable VPS hosts through Bedrock Ansible.
---

# Orchestra disposable workbench

Use this skill when Orchestra needs subagents to test or inspect a DevOps/Ensons-related repository on a temporary Ubuntu host. The current helper implements the Ensons test workflow; related repositories may add their own Bedrock role and test command under the same procedure.

## Non-negotiable boundary

The target repository is read-only during this workflow. Do not run `git commit`, `git push`, `git merge`, `git rebase`, `git worktree`, or any equivalent mutation in it. Do not ask a subagent to create a PR from it. A disposable clone is for inspection and testing only.

This skill authorizes inspection and testing only. If the task needs code changes, use a separate write-enabled workflow and checkout; do not relax the disposable-host boundary.

## Orchestrator conducts the work

The main Codex session owns the task, the user relationship, and all project-facing communication. Subagents are private workers that report to the orchestrator; they do not message the user or act as independent participants.

Before delegating, give each subagent one bounded assignment with the relevant user context, exact repository and revision, permitted actions, evidence to return, and a clear stopping point. Do not assume a subagent has loaded this skill: include the applicable read-only boundary and no-user-or-project-messaging rule in its actual prompt. Ask for a concise result with evidence and unresolved questions. Split work only when the assignments are independent; avoid overlapping investigations or sending one agent to every card or comment. In this procedure, subagents may inspect the disposable checkout and run the requested tests, but may not edit the target repository.

Stay responsible while the work runs. Check progress, redirect work that drifts, and give a focused follow-up when a report lacks evidence or leaves part of the assignment unanswered. If a missing decision blocks progress, bring the question to the user through the main session.

Review each report before relaying it. Check important claims against the checkout or captured command output, reconcile duplicate or conflicting findings, and ask for a targeted follow-up when needed. The orchestrator, not a subagent, decides what is relevant and sends one concise synthesis to the user.

## GitHub and Fizzy communication

Subagents must not create, edit, resolve, or delete GitHub or Fizzy comments, reviews, cards, assignments, or reactions. They may return evidence and proposed wording to the orchestrator. The orchestrator owns all such actions and follows the applicable GitHub review procedure and `netiquette-fizzy` skill.

For PR work, the orchestrator checks GitHub notifications and the live review threads first, then assigns bounded fixes for actionable feedback. Recheck the current PR head before accepting an agent's result; do not assume a notification or an earlier review snapshot is current.

Never resolve a review thread without either (a) changing and verifying the code to address the comment on the current PR head, or (b) replying with a concise, evidence-based explanation of why the requested change is unnecessary or already satisfied. If neither applies, leave the thread open and ask the user for direction.

For PR review work, have subagents return candidate findings with the changed file and line, concrete evidence, and a short suggested comment. The orchestrator checks existing review threads, removes duplicates, and prepares one batched inline review with the appropriate verdict. Show the exact draft and obtain the user's approval before submitting it. Do not turn subagent progress updates or findings into separate public comments.

The user owns merging. Never merge a PR unless the user explicitly authorizes that specific merge; requests to address, approve, or “land” changes do not authorize merging by themselves. When the user says they handle merges, leave the PR open for them.

Before approving a review or resolving a thread, verify each actionable request against the current code and tests. Do not treat a reply or rationale as a substitute for a requested code change.

## Provisioning

Use the executable helper in `scripts/run` for a repeatable Ensons run. It uses the official `/home/devops/.local/bin/hcloud` shim, which obtains the Hetzner token through the configured 1Password environment. The default host uses Ubuntu 24.04, `cx23`, `fsn1`, firewall `11690281`, and both SSH keys `agent-computer` and `orange.halo.hetzner.de`.

Hetzner charges a minimum of one hour for a server. Never delete a newly created server in the cleanup path before that hour has elapsed. Keep a failed host available for diagnosis and retry; print its ID and address so the operator can use it. Delete it only after the first billable hour, or when the operator explicitly accepts the wasted minimum charge.

Use the cheapest available x86 server in Falkenstein that meets the job's actual requirements. For the ordinary Ensons test workflow, that means at least 2 vCPUs, 4 GB RAM, and 40 GB disk; use `cx23` as the normal starting point and move up only when the run proves that it needs more. Do not default to a larger server for convenience, and do not use an ARM type when the repository's images or browser dependencies require x86.

## Remote changes and tests

For the current Ensons workflow, all changes on the temporary host go through the Bedrock Ansible role `ansible/roles/ensons-test` and `ansible/playbooks/agents.yml --tags ensons_test`. Related repository workflows must likewise use their Bedrock role and playbook. Do not install packages, copy secrets, clone the repository, or edit files with ad hoc SSH commands.

The role clones the live GitHub repository over SSH into `/data/ensons`, copies the controller-provided `.env.test` through Ansible, installs the pinned Ruby, Node, pnpm, and Playwright dependencies on the host, and starts only the repository's Docker Compose dependencies. Chromium and all tests run directly on Ubuntu.

Treat the Minitest summary as authoritative. The repository's `bin/tests` wrapper can return zero despite failures, so a successful Ansible exit is not a passing test unless both lanes report zero failures and zero errors. Report the exact summaries and representative root causes.

## Completion and cleanup

The workflow is complete only when the requested evidence was collected; host provisioning or a zero exit code alone is not enough. Retain the real test output. Server deletion is a separate cleanup action and must respect the first billable hour.
