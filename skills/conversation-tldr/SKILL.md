---
name: conversation-tldr
description: Use these conversation techniques when the user writes "tldr" and while acting as a pair programmer.
---

# Conversation TLDR

- Responses of cheap AIs are often too verbose. This skill provides some examples to rectify that.
- Derive your reporting style by adapting the BAD → GOOD translation in all responses.
- When the user writes "tldr", apply this skill henceforth in that conversation.

## Examples

### Overly detailed report

#### BAD - Wrong level of abstraction

- "All done" is a very red flag. The human alone decides whether a task, feature or project is "done".
- File checksums are irrelevant to the human.
- Whatever was meant by "fixes" was actually a bug introcuded (those tags should not have been added to git)
- Complicated grammar

```
All done. Here's what happened:

Ansible ran on web1 — tag openclaw_workspace:
- Created /data/openclaw/.openclaw/workspace/ directory
- Copied 4 files from agentic workspace as regular files (not symlinks)
- All assertions passed — files verified as regular files

Verification:
| File        | Size | Match |
|-------------|------|-------|
| AGENTS.md   | 364B | ✓     |
| SOUL.md     | 10B  | ✓     |
| IDENTITY.md | 36B  | ✓     |
| USER.md     | 106B | ✓     |

Files owned by openclaw:openclaw, permissions 0644, confirmed not symlinks.

Fixes discovered during ansible run:
1. Missing tags: [openclaw_workspace] on "Ensure workspace directory exists" task
2. Missing tags: [openclaw_workspace] on "Validate workspace files are regular files" task

Both fixed and committed to ensons/bedrock. All 4 repos (agentic, bedrock, telephony, skills) pushed.
```

#### GOOD - High-level overview

- High-level overview of what happened (categories of files, repository names)
- Surface hidden assumptions (the tag)
- Inform whether you have loose threads/open ends ("next steps")

```
# What I did

- I ran ansible on web1
- It created `/data/openclaw/.openclaw/workspace/AGENTS.md` etc.

# How I did it

- I tagged the ansible resources with `openclaw_workspace` so I can run them without individually (not the whole ansible run)
- Everything committed and pushed to 4 repos (agentic, bedrock, telephony, skills)

# Next Steps

Working on the refactoring you asked me about earlier. OK?
```

#### BAD - Implementation details

- Too long lines of text (hard to parse for humans)
- Difficult grammar/abbreviations (mixing sentences with code)
- Overconfident
- `ruby -c` is something to be done, but not reported

```
• Hermes — yes, has bobby skills now: hermes config get skills.external_dirs → - /Users/me/Code/2-Areas/skills/skills (only bobby, ~/.agents/skills removed) and hermes skills list shows 15 bobby skills as local
(coding-code-simplification, coding-ruby, investigate-*, procedure-*, etc.). Loaded via external_dirs + pwd bind-mount (pwd is mounted at same path).

• Docker error — cause: ~/.agents/bin/ai:111 (and :108):

  ─ ruby
    cmd += ['--mount', "type=bind,source=#{AGENTS_SOURCE.join('skills')},target=/Users/me/.grok/skills"]
    cmd += ['--mount', "type=bind,source=#{AGENTS_SOURCE.join('agents')},target=…"]

  unconditional. Since fef3ea9 removed ~/.agents/skills + agents/ (skills live in bobby/skills now), Docker fails with invalid mount config: bind source path does not exist: /Users/me/.agents/skills.

• Fix applied: made Grok/Opencode mounts conditional in bin/ai:
  • if File.exist?(AGENTS_SOURCE.join('skills')) → mount legacy, else if File.exist?('/Users/me/Code/2-Areas/skills/skills') → mount bobby/skills → ~/.grok/skills and → ~/.config/opencode/skills
  • same for agents (only mount if exists)
  • ruby -c bin/ai OK

No restart needed for Hermes (host-side), next bin/ai h/g/o will use new mounts.
```

#### GOOD - Categories of implementation

- To the point
- Defensive Verification ("seems to have worked")
- Actionable Summary at the end

```
# What I did

- Configured Hermes to respect `/Users/me/Code/2-Areas/skills/skills` (via `skills.external_dirs`)
- `hermes skills list` actually shows them (so it seems to have worked)

# Problems I detected

- Docker tried to mount `AGENTS_SOURCE.join('skills')` but that doesn't exist any more.

# Things I need to know from you

- In `Dockerfile`:
  - Stop trying to mount it
  - mount it conditionally (`File.exist?('/Users/me/Code/2-Areas/skills/skills'`)
```

