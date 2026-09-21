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

#### BAD

- WAY too long lines (human parsing impossible).
- Retelling all actions is like an intern reporting what he did without adding value or solutions.
- Listing the tests count is irrelevant ("Numbers:")
- Too verbose "Need your call" section

```
3.4.8 runs the suite again, and the order/follow-up tests in our area are green.

The 3.4.8 crashes weren't Ruby — they were corrupt native extensions in ~/.gem: msgpack, date (this was the real segfault: Date.new inside creek/book.rb), then puma→nio4r. gem pristine per gem, finally gem
pristine --all, fixed it. Two more environment repairs were needed: ~/.gem/bin/ruby was missing so every binstub was dead (bundle exec in rake tasks, rubocop) — symlinked to the mise 3.4.8 ruby; and the CSS
builds were absent, so every page 500'd with Propshaft::MissingAssetError — rails assets:build:css. .ruby-version stays 3.4.8; my bundle install only rewrote the lock's ruby/BUNDLED WITH lines back to 3.4.8 /
4.0.10.

Fixed

• The dead admin follow-up system test (screen, controller and route are gone) and the two dead admin routes (follow_up, assignment) — both admin controllers had already been deleted.
• Three admin views still linked to the removed admin assignment route (order _attributes, order _table, the waiting-for-assignment banner) → now the Meister screen via Core::Router::Url.for, like the
navigation.
• Meister follow-ups: with the redirect gone, an unassigned order hit the view's elsif and rendered an empty card title → falls back to the "du" wording. Test grown to 10 (renders, unassigned, other commander,
Heute/Morgen, today, past, far-future ± comment, within a week, bypass).
• overdue_assignment_test was time-of-day flaky — "wartet gestern" flips to "seit 2 Tagen" after noon; frozen at 10:00 with travel_to.
• Admin quotations controller test posted no follow_up_at, which is required on Quotation now → added.
• fixtures :all on the two Rack files that died on the DB-default company (orders_test RackTests, pay_in_advances).

Numbers: meister integration + models/validators 306 runs / 0 failures; meister + admin Rack system tests 5+5+2+9+1 all green; whole test/integration 209 runs with 2 failures, both ConnectorIntegrationTest DDNS  │
(502 from the external service).

Warn

• That FK failure is systemic, not local: orders.company_id/quotations.company_id carry a DB default (aeb1ceaf…) that only your new default_company fixture supplies, and 18 more system/integration files create
orders/quotations without fixtures :all. fixtures :all per file is what fixed my two, but it also changes what those legacy tests see (empty listings, etc.).
• Browser-based system tests can't start: the ruby client asks for chromium_headless_shell-1234, the cache has 1243, npx playwright is 1.62.1.

Need your call

• For those 18 files: fixtures :all each, or give the order/quotation factories a company { ::Core::Company.first || create(:company) } like the commander factory?
• I found no test that depended on the order form's follow_up_at field — if you saw a specific one fail, point me at it.
```

#### GOOD

- Do not mention normal setup steps (rebuilding gems, compiling assets)
- Mentions unorthodox solutions (symlinking ~/.gem/bin/ruby is a hack that needs to be reported)
- Differentiates test setup problems from code problems (important to know, did you try to appease the tests, or were the tests wrong?)

```
# What I did

- Tried Ruby `4.0.10` instead of `3.4.8`
- Tests related to our feature are green, (no browser tests, though, many others failed)
- Had to hack symlinks (`~/.gem/bin/ruby`)

# Tests I fixed

- Fixed links in views (routes pointing to the admin namespace rather than meister)
- Fixed controllers not accepting required parameters
- Some tests were flaky, but freezing time helped
- Some tests were missing `fixtures :all`, I added it

# Problems where I need your feedback

- There is a mismatch with the fixtures for company_id, that would need cleanup (not DRY)
- Playwright/Chromium has a version update problem, should I try to fix it myself?
```
