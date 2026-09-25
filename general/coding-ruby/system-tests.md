# System Tests — Rails Browser

This covers Rails' browser-level system testing (`ActionDispatch::SystemTestCase`).
For the general concept of system tests (full stack, real dependencies) see [`tests.md`](tests.md).
For simulated HTTP system tests (`ActionDispatch::IntegrationTest`) see [`test-framework.md`](test-framework.md).

## Rack vs Browser

System tests use one of two drivers. **Always use Rack unless you need JavaScript.**

| Driver | When to use | Speed |
|--------|-------------|-------|
| **Rack** | Standard CRUD, form submissions, simple navigation — no JS | Fast (no browser) |
| **Browser** | Complex JS, PDF generation, async operations, anything requiring JavaScript | Slower (Chromium/Playwright) |

Since each class can only use one driver, use a module wrapper with inner classes for files with mixed requirements:

```ruby
module AdminBriefingsTest
  class RackTests < Support::Test::Rack
    test 'CRUD operations' do
      # No JS needed
    end
  end

  class JsTests < Support::Test::Browser
    test 'PDF preview' do
      with_sidekiq
      # JS and Sidekiq required
    end
  end
end
```

## URL Helpers

**Always use `_url` with explicit host** in system tests. Avoid `_path` helpers.

```ruby
# Good — explicit host
visit admin_posts_url(backdoor_commander: true, host: ::Core::Router::Host.ensons)

# Bad — relies on default host
visit admin_posts_path
```

## Sidekiq Processing

Tests that trigger async jobs (emails, PDF generation) must call `with_sidekiq`:

```ruby
test 'sends email' do
  with_sidekiq

  click_on 'Send Email'
  assert_text 'Email sent'
end
```

## PDF Download Testing

Use the `downloading` helper to capture PDF downloads:

```ruby
test 'can download PDF' do
  visit admin_document_url(document, backdoor_commander: true, host: ::Core::Router::Host.ensons)
  click_on 'PDF Vorschau'

  download = downloading do
    click_on 'zum runterladen'
  end

  # PDF text is extracted for assertion
  assert download.pdf.include?('Expected content')
  assert download.pdf.include?('Seite1von1')
end
```

The `downloading` helper:
- Works in headless and headed modes
- Handles Playwright downloads vs new windows
- Returns metadata with `:pdf` (text content) and `:filename`
- Available via `Support::Download::Helper`

## Debugging

**Visible browser:**
```bash
CHROME=1 rails test test/system/admin/feature_test.rb
```

**Screenshot on failure:** Automatically captured in `tmp/downloads/`.

## Common Assertions

```ruby
# Content
assert_text 'Text on page'
assert_no_text 'Text not on page'
assert_includes body, 'html content'

# Navigation
assert_current_path expected_path

# Elements
assert_selector '.css-class'
assert_no_selector '.missing-class'

# Within scope
within('.sidebar') do
  assert_text 'In sidebar'
end
```

## Common Actions

```ruby
# Click
click_on 'Link text'    # works for links or buttons
click_link 'Link text'
click_button 'Button text'

# Forms
fill_in 'Field label', with: 'Value'
select 'Option', from: 'Dropdown label'
check 'Checkbox label'
uncheck 'Checkbox label'

# Date selection
select_date day: 20, month: 'Oktober', year: 2017, from: 'Label'

# Confirmation dialogs
accept_confirm { click_on 'Delete' }
accept_alert { click_on 'Action' }
```
