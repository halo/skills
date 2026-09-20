---
name: coding-commenting
description: How to phrase code comments. Use when writing or reviewing comments in any file type.
---

# Commenting

These rules are language-agnostic and apply to every file type that carries comments.

## Rules

- Write comments in sentences that each fit on one line.
  Avoid mid-sentence line breaks — humans read line-by-line to digest the information.
- Order comments chronologically when explaining a flow — first cause, then consequence.
  Never use reverse-temporal phrasing like "after B fires, A happens" or "A runs before B starts".
  State every event in the order it actually occurs: first A, then B, then C.
- After explaining the problem chronologically, explain the fix.
- Use unambiguous terms with precise roles (e.g. "the backend", "CloudFront", "the browser"),
  not vague ones like "it" or "the response header" that could refer to multiple layers.

## Examples

Examples use `#`; adapt the syntax to the language.

```text
# Bad — mid-sentence break:
# The browser only sends the Origin header on CORS-required requests (JS
# modules, fonts). CSS <link> requests omit it.

# Bad — "response header" is ambiguous (backend→CF? CF→browser?):
# CloudFront's response header rule only adds Access-Control-Allow-Origin
# when the Origin header is present.

# Bad — reverse-temporal phrasing ("before") buries the actual sequence:
# The browser fires the `load` event before Stimulus's importmap modules finish.
# Capybara returns from navigation before Stimulus starts.

# Good — one sentence per line, clear roles:
# Sometimes browser asset requests have no `Origin` header (e.g. CSS via <link>).
# The backend (Rails) then omits `Access-Control-Allow-Origin` from its response.
# CloudFront passes the backend response to the end-user as-is.

# Good — chronological order: problem sequence, then fix sequence:
# The browser loads the page and fires the `load` event.
# Playwright reports navigation is complete.
# Importmap module scripts such as Stimulus are still being fetched and executed.
# Clicking a Stimulus-controlled element fires the click event.
# But Stimulus has not registered its event delegation listener yet.
# The Stimulus action handler never fires.
#
# We poll for `window.Stimulus` before each click.
# Once Stimulus is defined, we proceed with the click.
# Stimulus's event delegation listener catches the click and the action handler fires.
```

## Red Flags

- Commenting how code used to work, rather than the current implementation (e.g. "This used to connect over less performant HTTP but now it uses websockets.").
