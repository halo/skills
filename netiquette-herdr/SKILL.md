---
name: netiquette-herdr
description: Run local and remote commands in Herdr workspaces, tabs, and panes, so sub-processes are persistent and the user can see and follow the work.
---

# Herdr

The Herder CLI usage skill can be read with `herder --skill` in stdout.

A missing `HERDR_ENV` in a shell subprocess alone does not establish that Herdr has no caller context. Codex commands can lose pane-injected environment variables even while running in a Herdr pane ([issue #1582](https://github.com/herdrdev/herdr/issues/1582)). Herdr also has a related report about Codex 0.157 losing session identity; that report describes a different symptom ([issue #4649](https://github.com/herdrdev/herdr/issues/4649)).

When the variable is absent, ask Herdr for the current pane and use its response instead of concluding that the caller is outside Herdr. Herdr's [integration documentation](https://herdr.dev/docs/integrations/) describes the pane environment variables available to child processes.

## Choose a level of abstraction for the work

* A `Workspace` is the top-level project container. Use one per repo or investigation.
* A `Tab` is a layout inside a workspace. Use tabs to separate views like `agent` for the main harness, `commands` for running ephemeral tasks, `tests` for running the test suite, etc.
* A `Pane` is a Terminal inside a Tab. Dedicated Tabs like `agent` or `commands` usually only need one pane. Complex tabs like `tests` or `review` might have sub-panes such as `minitest` + `logs`, or `diff` + `readme`, or `plan` + `example code`.

## Keep work organized

* Never reuse or close a pane containing solely the user's work.
* A Tab with an `agent` harness can be split at most with one secondary pane. It's helpful for the user to see what you're shelling out to while thinking. Re-use that pane rather than creating more panes in the `agent` tab.
* Panes used for ephemeral tasks (such as listing files, diffs, CLI experimentation) should be closed again, if there is no work inside valuable to the user.
* When the user asks for code examples or diffs, it's a good idea to show it in a secondary pane like a sidecar (with syntax highlighting).
* For new panes, preserve the intended working directory and use `--no-focus` unless the user asks to switch focus.

## Work on remote servers

* Do not replace a failed Herdr connection with an invisible shell or standalone SSH session. Report the actual connection, authentication, or version error. Only add, remove, enable, disable, install, or replace Herdr machine profiles or servers when the user has asked for that change.
* Run Ansible from a visible Herdr pane on its controller and limit it to the intended inventory targets. Ansible's SSH connections to those targets are part of the playbook run; do not SSH back into the controller when local execution is appropriate.
* For interactive work directly on a lab machine, use its saved Herdr machine profile and run the shell or agent in a remote Herdr pane. This lets the user follow the target's work through their Herdr connection.

## Operate panes explicitly

* Read pane and layout IDs from Herdr responses; do not guess them or rely on whichever pane is focused.
* Discover the caller with `herdr pane current --current` and inspect its tab layout when needed. Use the returned pane ID for subsequent operations.
* Some operations such as `pane split --current` require `HERDR_PANE_ID`; when that variable is absent, pass the explicit pane ID returned by Herdr instead.

Create panes without taking focus, then run commands in the returned pane ID:

```sh
herdr pane split --pane <caller-pane-id> --direction right --cwd "$PWD" --no-focus
herdr pane run <new-pane-id> 'the command to run'
herdr pane read <new-pane-id> --source recent-unwrapped --lines 100
```

Use `pane wait-output` for long-running commands and then read the pane output. Treat timeouts as unknown completion: inspect the pane before retrying a command that changes state.
