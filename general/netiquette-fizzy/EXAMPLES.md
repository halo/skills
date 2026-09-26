# Fizzy comment examples

Read this with `SKILL.md` when writing or reviewing comments. The examples show the intended level of abstraction and the expectation that work continues when the next action is clear.

## Comment decision

```text
# BAD
Ran the entire test command from directory X; it produced N runs, M assertions,
zero failures, and this exact log line: ...

# GOOD
### Outcome

Import retry handling is working in automated coverage.

- Transient failures are retried and successful imports stop retrying.
- The retry decision is now recorded in the card's implementation contract.

### Next

I’m running live verification now.
```

## Proactive, reusable memory

```text
# BAD (Too many details, not actionable)
Live-call evidence: in call 35836a4d, the request to speak English produced an inability response. An isolated Qwen run then called the installed language tool, but the plugin returned a read-only SQLite error because /home/phone/telephony/preferences.sqlite3 is phone:phone 0644. See new cards 107 and 108 for the two separate failures.

# GOOD (The cause and the next action are clear)
The LLM could not switch to English because it had no permissions to write to the preferences file. Fixing the permissions right now (tracking in https://app.fizzy.do/6181285/cards/107)...
```

## Answer the current question

```text
# BAD (Not informative enough for a comment and does not relate to the question)
Card title: "Telephony: real SIP regression matrix"
Human comments: "Do we want \"I am waiting\"? How is it triggered? What's the mechanism?"
AI replies: "Inspected the last three real calls: 35836a4d (German→English request, no language change), 88494445 (interim Wie ist das weg? corrected to Wie ist das Wetter in Berlin?), and 5e006e14 (duplicate favorite-color transcript). SST calls took 2.4–3.7 seconds and every call ended with transport EOF after the caller disconnected."

# GOOD
### Finding

Card title: "Telephony: Robust test regressions"

Inspected the last three real calls involving language switching. The SST took up to 3.7 seconds; I’m tracing that delay now.
```

## Summarize verification

```text
# BAD (wrong level of abstraction)
Verification evidence: dispatcher go test ./... is green. test/bin/unit is 22 runs, 76 assertions, 0 failures, errors, or skips. The real SIP case test/bin/system --name=/test_one_call_hangs_up_when_the_model_is_asked/ is 1 run, 6 assertions, 0 failures, errors, or skips. The call audit identified one terminal outcome: the model requested hang-up, actor model, reason phone_hangup; no late provider speech occurred.

# GOOD
### Verification

Unit and system tests are green (real SIP calls).

Two issues resolved:
- The assistant now says "Good bye" before hanging up.
- After hangup, the LLM does not attempt to speak any further.
```
