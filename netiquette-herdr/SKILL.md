---
name: netiquette-herdr
description: Run task commands in Herdr panes, tabs, or workspaces so the user can see and follow the work.
---

# Visible command work in Herdr

Run shell commands for task work from a Herdr-managed pane, tab, or workspace visible to the user. Keep the active conversation pane available for coordination and review.

## Choose a place for the work

- For one foreground operation, create a sibling pane in the current tab. Preserve the intended working directory and use `--no-focus` unless the user asks to switch focus.
- For several related commands or concurrent operations, use a separate tab. Use a workspace for a distinct project or longer-running context.
- Do not reuse or close a pane containing the user's work. Read pane and layout IDs from Herdr responses; do not guess them or rely on whichever pane is focused.
- Run read-only shell checks in the task pane too. Use direct file-editing tools for file changes; use the pane for commands that inspect, validate, build, test, or change state.

## Run work on lab machines

- Run Ansible from a visible Herdr pane on its controller and limit it to the intended inventory targets. Ansible's SSH connections to those targets are part of the playbook run; do not SSH back into the controller when local execution is appropriate.
- For interactive work directly on a lab machine, use its saved Herdr machine profile and run the shell or agent in a remote Herdr pane. This lets the user follow the target's work through their Herdr connection.
- Do not replace a failed Herdr connection with an invisible shell or standalone SSH session. Report the actual connection, authentication, or version error. Only add, remove, enable, disable, install, or replace Herdr machine profiles or servers when the user has asked for that change.

## Operate panes explicitly

Discover the caller with `herdr pane current --current` and inspect its tab layout when needed. Use the returned pane ID for subsequent operations. Some operations such as `pane split --current` require `HERDR_PANE_ID`; when that variable is absent, pass the explicit pane ID returned by Herdr instead. A missing `HERDR_ENV` in a shell subprocess alone does not establish that Herdr has no caller context; ask Herdr for the current pane and use its response.

Create panes without taking focus, then run commands in the returned pane ID:

```sh
herdr pane split --pane <caller-pane-id> --direction right --cwd "$PWD" --no-focus
herdr pane run <new-pane-id> 'the command to run'
herdr pane read <new-pane-id> --source recent-unwrapped --lines 100
```

Use `pane wait-output` for long-running commands and then read the pane output. Treat timeouts as unknown completion: inspect the pane before retrying a command that changes state.
