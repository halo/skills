# Test Support Base Classes (Rails)

All base classes live under `test/support/test/` and are organized in an inheritance hierarchy.

## Inheritance chain

```
ActiveSupport::TestCase
  └── Support::Test::Unit                    # Models, services, formatters

ActionDispatch::IntegrationTest
  └── Support::Test::Integration              # API endpoints, multi-request flows

ActionDispatch::SystemTestCase
  └── Support::Test::System                  # Shared system test behavior
        ├── Support::Test::Rack              # No JavaScript
        └── Support::Test::Browser           # With JavaScript

ActionMailer::TestCase
  └── Support::Test::Mailer                  # Email delivery
```

## `test/support/test/unit.rb`

For models, services, formatters, validators tested in isolation.

```ruby
module Support
  module Test
    class Unit < ActiveSupport::TestCase
      include FactoryBot::Syntax::Methods
    end
  end
end
```

## `test/support/test/integration.rb`

For API endpoints, controller tests, routing tests — single or multi-request HTTP workflows.

```ruby
module Support
  module Test
    class Integration < ActionDispatch::IntegrationTest
      include FactoryBot::Syntax::Methods
    end
  end
end
```

## `test/support/test/system.rb`

Shared behavior for all system tests. Include email helpers, download helpers.

```ruby
module Support
  module Test
    class System < ActionDispatch::SystemTestCase
      include FactoryBot::Syntax::Methods
      include Capybara::Email::DSL
      include Support::Download::Helper
    end
  end
end
```

## `test/support/test/rack.rb`

For system tests without JavaScript. Faster — no browser launched.

```ruby
module Support
  module Test
    class Rack < ::Support::Test::System
      driven_by :rack_test

      # Rack cannot click on JavaScript modals.
      # Defining this no-op allows seamless switching to a driver that can.
      def accept_confirm
        yield
      end
    end
  end
end
```

## `test/support/test/browser.rb`

For system tests with JavaScript. Uses Playwright/Chromium. Disables transactional fixtures (use DatabaseCleaner instead).

```ruby
module Support
  module Test
    class Browser < ::Support::Test::System
      driven_by :playwright, using: :chromium,
                             screen_size: [1200, 1000],
                             options: {
                               headless: ENV['CHROME'].blank?,
                               timeout: (ENV['CI'] ? 10_000 : 2_000),
                             }

      self.use_transactional_tests = false

      def setup
        super
        DatabaseCleaner.strategy = :deletion, { pre_count: true }
      end

      def teardown
        DatabaseCleaner.clean
        super
      end
    end
  end
end
```

Run with visible browser: `CHROME=1 rails test`.

## `test/support/test/mailer.rb`

For testing email delivery.

```ruby
module Support
  module Test
    class Mailer < ActionMailer::TestCase
      include FactoryBot::Syntax::Methods
    end
  end
end
```


