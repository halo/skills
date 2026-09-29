---
name: netiquette-skills
description: Choose and apply the correct skill namespace for the task.
---

# Skill namespaces

Use the namespace to signal the abstraction level and load the smallest relevant skill.

- `procedure-*` describes how to approach a problem conceptually: planning, sequencing, implementation, research, or testing.
- `netiquette-*` describes conventions for interacting with a tool, product, service, or team workflow.
- `coding-*` describes implementation and code-quality rules, such as style, naming, and lint-like constraints.
- `investigate-*` supports bilateral exploration: brainstorming, interviewing, clarification, and shaping an uncertain problem with the human.

Keep the levels separate. A `procedure-*` skill should not become a tool manual; a `netiquette-*` skill should not prescribe a general problem-solving method; a `coding-*` skill should not define product etiquette; and an `investigate-*` skill should not silently decide unresolved user preferences.

When several levels apply, use the minimal combination in this order:

1. `investigate-*` when the problem or user preference is still unclear.
2. `procedure-*` when the approach needs structure.
3. `coding-*` for implementation constraints.
4. `netiquette-*` for tool-specific interaction rules.

Prefer a focused skill over a catch-all. Name new skills `namespace-topic`.
