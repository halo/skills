---
name: coding-ansible
description: Some rules we have for ansible rules.
---

# Ansible

# General advise

- Writing changes to ansible roles is first "complete", when an actual ansible run did what the change intended.

# Do's and Don'ts

- Do not use Ansible to clean up old state, like when you renamed a service, don't add a step to stop the old service, that's just noise. Instead, kill the service manually and then run ansible to establish the new service.
