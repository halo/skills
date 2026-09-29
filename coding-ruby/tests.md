# Testing (Ruby)

## Test Types

Two categories apply universally to both Rails and pure Ruby projects:

| Type | Scope | Dependencies |
|------|-------|-------------|
| **Unit** | Single class or module in isolation | Boundaries mocked/stubbed |
| **System** | Full stack, everything together | Real — S3 buckets, shell commands, actual IO |

**Rails** adds a fidelity split within system tests (both are "full stack", just different drivers):
- Simulated system tests (`ActionDispatch::IntegrationTest`) — faster, no browser, multi-request workflows
- Browser system tests (`ActionDispatch::SystemTestCase`) — slower, real DOM, JS execution

See [`test-framework.md`](test-framework.md) for inheritance and [`system-tests.md`](system-tests.md) for browser specifics.

**Pure Ruby** doesn't have the simulated-middle layer — system tests go straight to real infrastructure.

## Framework Choice

- **Rails projects:** Always use `minitest`.
- **Pure Ruby projects:** Always use `megatest`.

## Test Structure

**Never use `require` or `require_relative` in test files.** `test_helper.rb` is loaded automatically by the test runner (megatest in pure Ruby, or `require 'test_helper'` in Rails). Use `setup`/`teardown` to isolate state. Reset global module state in `teardown`.

### Arrange-Act-Assert with empty line separators

Structure every test block in three sections separated by blank lines:

```ruby
test '#name - works' do
  # (1) Setup / arrange — create objects, set up data
  thing = build(:thing)

  # (2) Act — the single action being tested
  result = thing.call

  # (3) Assert — verify the outcome
  assert_equal expected, result
end
```

When the setup is a single-line action (e.g. creating a record), it can share the setup section:

```ruby
test '#first - with no customers - returns nil' do
  customer = create(:customer)

  result = Customer.first

  assert_nil result
end
```

```ruby
class SmokeTest < Megatest::Test
  setup do
    @original_env = ENV.to_hash
    ENV['RAILS_ENV'] = 'development'
    ENV['POSTGRES_PASSWORD'] = 'test'
  end

  teardown do
    ENV.clear
    ENV.update(@original_env)
    Capsule.instance_variable_set(:@config, nil)
  end

  test 'config knows the stage' do
    assert_equal 'development', Capsule.config.stage
  end
end
```

## Naming

### Test Method Names

Use block syntax with `test`:

```ruby
test 'allows valid domain' do
  # ...
end
```

**Pattern:** `[Method/Context] - [Condition] - [Expected behavior]`

```ruby
# Simple case
test '.call - creates a report'

# With context
test '.call - with valid params - creates a report'

# Instance method
test '#name - with user - is the name of the user'

# Multiple conditions
test '.call - with even number - omits zero'
```

Condition first, then behavior. Not: `omits zero with even number`.

### Avoid Vague Words

Do not use: "returns", "should return", "gives", "provides".

```ruby
# Bad
test '.endpoint - returns localhost'
test '#total - returns calculated value'

# Good
test '.endpoint - is localhost'
test '#total - is sum of line items'
```

### Guidelines

- Lowercase except proper nouns
- Present tense (not past)
- No "should" or "must" — state behavior directly
- Keep under 80 characters when possible
- Use hyphens to separate parts (spaces around hyphens)

### Class Names

**Use flat class names at root level** — avoid nested modules:

```ruby
# Good
class AdminQualitySnapshotsTest < Support::Test::Rack
end

# Bad — nested modules cause conflicts
module Admin
  module Quality
    class SnapshotsTest < Support::Test::Rack
    end
  end
end
```

**Pattern:** `[Scope][Feature][Type]Test`

```ruby
AdminPostsTest              # Admin posts CRUD
ApiUsersControllerTest      # API users controller
FormattersEuroTest          # Euro formatter
CalculatorsPriceDifferenceTest  # Price difference calculator
```

## Assertions

### Minitest Basics

```ruby
# Equality
assert_equal expected, actual

# Nil / Empty
assert_nil value
assert_empty collection

# Boolean
assert predicate
refute predicate

# Errors
assert_raises(Error) { code }
```

See the [minitest documentation](https://docs.seattlerb.org/minitest/) for the full list.

### Tron Results

Never use `assert_predicate result, :success?` when testing `Tron` objects.
Instead assert on the specific tag to ensure the expected code path was taken.

```ruby
# Bad — only checks boolean state, not which path was taken
assert_predicate result, :success?

# Good — asserts the exact tag
assert_equal :expected_code, result.success
assert_equal :expected_code, result.failure
```

### Rails-Specific Assertions

```ruby
assert_response :success          # HTTP response status
assert_redirected_to root_path    # Redirect target
assert_routing '/admin', controller: 'admin/dashboard', action: 'index'
assert_difference 'User.count', 1 { post :create, params: {...} }
```

## Syntactic Sugar

In tests, you may use refinements to simplify code by refining core classes.

```ruby
module QuotingRefinement
  refine String do
    def quote
      "\"#{self}\""
    end
  end
end

using QuotingRefinement

puts "hello".quote  # only available where we called using
```
