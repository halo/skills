---
name: coding-ansible
description: Conventions for maintainable Ansible roles, playbooks, paths, and real-run verification.
---

# Ansible

# General advise

- Writing changes to ansible roles is first "complete", when an actual ansible run did what the change intended.
- Keep playbooks in the Ansible repository under `ansible/playbooks/`. Skills and application repositories may invoke Ansible, but must not carry their own playbooks.

# Paths and variables

- Prefer the established fixed server paths directly in tasks and templates. They are part of the machine contract, not configuration knobs.
- Do not add a variable to `roles/*/defaults/main.yml` when it is used in only one place. Inline the value instead; one-use indirection is strictly forbidden in first-party roles.
- Keep a variable only when it represents a real supported variation, is used in multiple places, or is an intentional public role input. Before adding one, search all references and state why callers need to override it.
- When an existing one-use variable adds no real choice, replace it with the fixed value and remove the default. A focused mass search-and-replace is preferable to preserving needless configuration surface.

# Do's and Don'ts

- Do not use Ansible to clean up old state, like when you renamed a service, don't add a step to stop the old service, that's just noise. Instead, kill the service manually and then run ansible to establish the new service.
- Do not hide failures with `failed_when: false`, `ignore_errors`, or `rescue`/`always` cleanup paths. Ansible runs must fail at the first unexpected state; use assertions and explicit preconditions when a state is required.
- A `failed_when` expression is appropriate only when it makes a concrete validation stricter than the module's normal failure behavior. It must never turn an expected command failure into success.
