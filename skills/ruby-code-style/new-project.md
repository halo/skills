---
name: ruby-new-project
description: Starting a new Ruby project. Use when bootstrapping a fresh Ruby codebase.
---

# New Ruby Project

## Gemfile

Every project starts with this Gemfile:

```ruby
# frozen_string_literal: true

source 'https://rubygems.org'

gem 'rubocop', group: :development
```

`rubocop` is required so that VSCode's Ruby LSP extension can provide linting and formatting. Do not skip it.

### Framework-Specific Additions

**Pure Ruby:**
```ruby
group :test do
  gem 'megatest'
end
```

**Rails:**
```ruby
group :test do
  gem 'minitest'
end
```

## File Layout

```
lib/
  my_project/
    # implementation
test/
  # tests
bin/
  # executables
Gemfile
```

## First Steps

1. `bundle install`
2. Write a smoke test
3. Run the smoke test: `bundle exec ruby -Itest test/smoke_test.rb`
