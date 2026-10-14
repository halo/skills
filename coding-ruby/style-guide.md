# Style Guide (Ruby)

## General Rules

- Never use `class << self`. Always use explicit `def self.method_name`.
- Never use short or abbreviated variable names (`cp`, `x`, `i`, `rec`, `el`). Use full, descriptive names (`claimable_capability`, `record`, `index`, `element`). The name must make the variable's purpose obvious without the reader having to look at the collection it came from.
- ActiveRecord scopes must always be prefixed with `scope_` (e.g. `scope :scope_unconsumed`, `scope :scope_active`). Never define a scope without the `scope_` prefix.

```ruby
# Bad
scope :unconsumed, -> { where(consumed_at: nil) }

# Good
scope :scope_unconsumed, -> { where(consumed_at: nil) }
```

## Look for Scopes Before Writing Raw ActiveRecord in Controllers

Before writing raw `where`/`order` chains in a controller, check the model for an existing scope that expresses the intent (e.g. `sorted_scope`, `with_employee_flag_scope`). Prefer chaining model scopes over inventing inline query logic; if no scope exists yet, add one to the model and use it in the controller.

```ruby
# Bad — raw ActiveRecord in controller
::Commander.where(company_id: current_company.id)
           .with_flag_scope(::Flag.employee)
           .order(:name)

# Good — named scopes on the model
::Commander.in_company_scope(current_company)
           .with_employee_flag_scope
           .sorted_scope
```

## File Placement (Zeitwerk)

When using Zeitwerk, every module and class must live in a file that matches its constant path. A nested constant requires a nested directory.

```ruby
# Good — Gewerkmeister::Connections::Check lives at the expected path
# lib/gewerkmeister/connections/check.rb
module Gewerkmeister
  module Connections
    module Check
    end
  end
end

# Bad — Gewerkmeister::Connections::Check defined inside connections.rb
# breaks Zeitwerk's implicit constant loading
# lib/gewerkmeister/connections.rb
module Gewerkmeister
  module Connection # <- Not optimal, prefer plural when nesting modules (to avoid accidentally opening a class)
    module Check  # <- Wrong file! Should be in connections/check.rb
    end
  end
end
```

### Supporting Classes

Do not reopen an existing class just to nest a supporting class beneath it. Define the helper in its own file under a purpose-named module instead. This avoids superclass mismatches and load-order coupling between files.

```ruby
# Bad — reopening a singular class to nest the supporting class
class Order
  class Summary
  end
end

# Good — a plural module, with the supporting class in its own file
# app/models/orders/summary.rb
module Orders
  class Summary
  end
end
```

## Modern Ruby (3.4+)

Always target Ruby 3.4+. Use `it` for single-argument block parameters instead of explicit `|arg|`.

```ruby
# Bad — verbose explicit block param
list.any? { |item| item.active? }

# Good — Ruby 3.4 `it` block param
list.any? { it.active? }

# Good — still acceptable for multi-arg or clarity
data.map { |key, value| "#{key}: #{value}" }
```

### No vertical alignment

Never pad values with whitespace to align hash literals or assignment columns.

```ruby
# Bad — vertical alignment
ICON_NAMES = {
  up:   :up_circle,
  down: :down_circle
}.freeze

# Good — natural spacing
ICON_NAMES = {
  up: :up_circle,
  down: :down_circle
}.freeze
```

## Service Objects with `calls`

Use the `calls` gem for the method object pattern. It combines `dry-initializer` with a `.call` convention.

```ruby
class BackupDatabase
  include Calls

  option :database_name
  option :compress, default: -> { true }

  def call
    # work
  end

  private

  # Prefer lazy-loaded vars over obvious option defaults.
  def logger
    @logger ||= Logger.new($stdout)
  end
end

BackupDatabase.call(database_url: "postgres://...")
```

### Rules for `calls`

- Use `param` only when the method object takes a single argument. Otherwise use `option`s.
- Avoid excessive use of options. Fewer input arguments are clearer.
- `attr_accessor` in Calls method objects must always be placed in the `private` section.

## Presenters

Presenters answer view questions. Two shapes exist — pick by usage:

- **Query object (PORO, no `Calls`)**: when the view asks *several* questions of the same context
  (`scheduled?`, `message`, `status`, …). Instantiate it and call methods freely.
  It is stateful with respect to its inputs and memoizes derived values.

  ```ruby
  # app/models/personnel/on_calls/notice.rb — a query object is a model, NOT a presenter.
  # app/presenters/** is exclusively for view presenters.
  module Personnel
    module OnCalls
      class Notice
        def initialize(commander:, company:) ... end

        def scheduled? = ...
        def message    = ...
      end
    end
  end
  ```

- **Calls processor**: only when there is exactly one question with one answer
  (`TableStatusPresenter.call(duty) => Entities::Status`). One `.call`, one return value.

Never mix the two: a presenter that `include`s `Calls` but returns `self` from `call` so views can
call predicates on it is an anti-pattern — it hides private API behind a processor interface.

## Executables (`bin/`)

Executable scripts in `bin/` must follow a strict pattern: require exactly one base library file, which handles all its own dependencies. Never require library files directly from executables.

```ruby
#!/usr/bin/env ruby
# frozen_string_literal: true

ENV['BUNDLE_GEMFILE'] ||= File.expand_path('../Gemfile', __dir__)
require 'bundler/setup'
require 'dotenv/load'

require_relative '../lib/scheduler'   # ← the only require

Scheduler.call(occasion:)
```

The base library (`lib/scheduler.rb`) is responsible for requiring all sub-modules, config, and dependencies it needs:

```ruby
# lib/scheduler.rb
require 'calls'
require_relative 'scheduler/config'
require_relative 'scheduler/enqueue'
```

### Scripts Fail Loudly

A script either does its job or dies — there is no third path of continuing with a `nil`.

- Never guard a required file, secret, argument or command (`File.exist?`, `|| ''`, `&.`, `rescue nil`) and then carry on with a `false`/`nil` value.
- Bail where the failure happens; the exception naming the missing path or value is the error report.
- `warn` plus a fallback only with a reason that survives review.

```ruby
# Bad — reads nothing, the failure surfaces later as a nil somewhere else
token = File.read('/run/secrets/op_token') if File.exist?('/run/secrets/op_token')

# Good — raises ENOENT and names the file
token = File.read('/run/secrets/op_token')
```

## Keyword-Argument Forwarding (Ruby 3.0+)

When a keyword argument name exactly matches the variable being passed, use the short-hand `name:` syntax. Never repeat the same identifier twice.

```ruby
# Bad — repeats the identifier
Scheduler.call(occasion: occasion)
UserMailer.with(user: user).confirmation.deliver_later

# Good — forward keyword
Scheduler.call(occasion:)
UserMailer.with(user:).confirmation.deliver_later
```

## Return Objects with `tron`

Use the `tron` gem for immutable return value patterns.

```ruby
class CreateContactAddress
  include Calls

  option :contact_address

  def call
    do_check_validity
      .on_success { do_post }
      .on_success { do_parse }
  end

  private

  attr_accessor :response

  # Actions start with `do_` and come above other methods.
  def do_check_validity
    return Tron.success(:valid_record) if contact_address.valid?

    log { 'Something went wrong' } # Determine if we need to inform about this.
    Tron.failure(:invalid_record)
  end

  def do_post
    # Store intermediate results in private accessors.
    self.response = HTTP.post('/contact-addresses', json: contact_address.to_h)

    if response.status == 201
      Tron.success(:contact_address_created)
    else
      Tron.failure(:contact_address_creation_failed, message: response.body['error'])
    end
  end

  def do_parse
    Tron.success(:parsed, id: response.body['id'])
  end

  # Helper methods come after the `do_` action methods
  def log(...)
  end
end
```

- Return `Tron.success(:tag, metadata...)` on success.
- Return `Tron.failure(:tag, metadata...)` on failure.
- Chain with `.on_success` and `.on_failure`.
- Use early-return guards with `return result if result.failure?`, not `return result unless result.success?`.

## Combining `calls` and `tron`

- When a service object uses both `calls` and `tron`, extract each step into a private method prefixed with `do_`. Chain them in `call`.
- `do_` methods never take arguments. If a value produced by one action is needed by a later action, store it in an `attr_accessor`.
- When a value must be shared between `do_` actions, use private `attr_accessor` and write via `self.` instead of `@`. A typo like `@response` silently returns `nil`, but `self.response` raises `NoMethodError`.

##

Unless in the Ruby root namespace, fully qualify Class and module names you're calling. That makes it:

- Easier to search and replace
- Easier for humans to detect how "far away" the call is leading
- Safer dynamic class name resolution

```ruby
# Good
module Outer
  module Inner
    def self.speak
      ::Whatever::Else.new # Good practice for a monolith
    end
  end
end

# Bad
module Outer
  module Inner
    def self.speak
      Whatever::Else.new # Ambiguous, is it in Outer, Inner or at the root?
    end
  end
end
```

## Commenting

Commenting rules are language-agnostic and live in their own skill:
read [`coding-commenting/SKILL.md`](../coding-commenting/SKILL.md) whenever you write or review comments.

## Method structure in Rails Controllers

- First CRUD methods (index, show, etc. as rubocop suggests)
- Private methods start with methods used by `before_action` (in that order) e.g. `def find_user`
- If, say, an `index` action outsource-calls methods like `index_html` and `index_json` then those come FIRST among private methods.
- Then `def scope` (if needed)
- Then any other private methods (convenience helpers)
- At last `def permitted_attributes` and `def some_model_name_params`
