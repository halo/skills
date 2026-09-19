# Ruby API Client Examples

## General Rules

* Prefer the `httpx` gem (over `httparty`, `faraday`, `rest_client` etc.)

## File Architecture

- If the API client is a standalone gem project, use the root folder of the project.
- If the client is added to an existing app codebase, create a dedicated folder (e.g. `app/gems/myclient`)

```shell
# Models are plain Ruby or ActiveModel objects that have attributes.
myclient/models/
myclient/models/person.rb
myclient/models/address.rb

# Builders take attributes (mostly scalar variables) and instantiate a Model with attributes.
myclient/builders/
myclient/builders/person.rb
myclient/builders/address.rb

# Parsers take whatever the upstream API sent (json, html, etc) and instantiate a Model with them.
myclient/parsers/
myclient/parsers/person.rb
myclient/parsers/address.rb

# Resources are what the remote server calls their REST models. They perform the remote call.
# They return data in the upstream format (json, html, etc)
myclient/resources/
myclient/resources/persons/index.rb
myclient/resources/persons/download.rb
myclient/resources/addresses/validate.rb
myclient/resources/addresses/upload.rb

# Actions are what we call our preferred REST actions, they can combine multiple resource calls
# They use the Builders and Parsers so that an Action only returns Models and response wrappers.
myclient/actions/
myclient/actions/persons/show.rb
myclient/actions/persons/create.rb
myclient/actions/addresses/destroy_all.rb
myclient/actions/addresses/validate.rb

# The class that is responsible for the connection state.
myclient/client.rb   # When using HTTPX
myclient/agent.rb    # When using for Mechanize

# Other basic configuration classes.
myclient/endpoints.rb
myclient/environment.rb
```

- **`Actions`** — generic CRUD operations (`Index`, `Create`, `Destroy`) that work across different endpoints.
- **`Resources`** — model-specific operations tied to a single remote entity (e.g. `Sims::GetSimList`, `Carriers::Find`).

## HTTP Basic Auth

When building the `Authorization` header for Basic auth, use `Base64.strict_encode64` — not `Base64.encode64`. The latter inserts a newline every 60 characters, which silently produces a malformed header value that the server rejects.

```ruby
# Good — single-line Base64
"Basic #{Base64.strict_encode64("#{username}:#{password}")}"

# Bad — contains embedded newlines, breaks the header
"Basic #{Base64.encode64("#{username}:#{password}")}"
```
```
