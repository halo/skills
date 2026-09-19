# Test File Layout (Ruby)

## Rule 1: Mirror source files as unit tests

Create one test file per source file, preserving the directory structure under `test/`.

### Pure Ruby

| Source file | Test file |
|---|---|
| `lib/<file>.rb` | `test/<file>_test.rb` |
| `lib/<dir>/<file>.rb` | `test/<dir>/<file>_test.rb` |

Examples: `lib/myapp.rb` → `test/myapp_test.rb`, `lib/myapp/translator.rb` → `test/myapp/translator_test.rb`.

### Rails

| Source directory | Test directory |
|---|---|
| `app/models/` | `test/models/` |
| `app/controllers/` | `test/controllers/` |
| `app/mailers/` | `test/mailers/` |
| `app/channels/` | `test/channels/` |
| `app/jobs/` | `test/jobs/` |
| `app/mailboxes/` | `test/mailboxes/` |
| `app/views/` | `test/views/` |

Example: `app/models/user.rb` → `test/models/user_test.rb`.

## Rule 2: Non-mirrored test folders

These folders contain tests organized by feature or scenario, not by source file. Create one file per workflow.

| Folder | Contents |
|---|---|
| `test/integration/` (Rails) | One file per multi-request workflow (e.g. `checkout_flow_test.rb`) |
| `test/system/` (Rails) | One file per browser-tested feature (e.g. `admin_posts_test.rb`) |
| `test/system/` (Pure Ruby) | One file per full-stack workflow — shells out, hits real services (e.g. `full_pipeline_test.rb`) |
| `test/fixtures/` (Rails) | One YAML file per model (`users.yml`, `articles.yml`). Binary files in `test/fixtures/files/`. |

## Rule 3: Test support files

`test/test_helper.rb` is always required. Additional support code lives under `test/support/` — see [`test-support.md`](test-support.md) for Rails base classes and helpers.

## Summary: decision flow

When adding a test file:

1. Is it a base class or helper? → **Rule 3** — place in `test/support/test/`
2. Does it test a single source file? → **Rule 1** — mirror the path under `test/`
3. Does it test a cross-cutting workflow? → **Rule 2** — place in the appropriate non-mirrored folder
