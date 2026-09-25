---
name: netiquette-markdown
description: How to compose informative Markdown.
---

# Markdown

Generally, the [Commenting](../coding-commenting/SKILL.md) and [TLDR](../conversation-tldr/SKILL.md) skills apply for markdown as well.

### Examples

##### BAD

* Too narrow columns
* List items not as list
* No blank lines
* Too many details (exact LUFS values)

```
- Call `88494445` produced `Wie ist das weg?` and then the corrected
  `Wie ist das Wetter in Berlin?`; call `5e006e14` repeated the same favorite
  color transcript. STT passes took about 2.4–3.7 seconds, and the caller
  disconnected while the provider was still working.
- The findings are tracked in Fizzy [107](https://app.fizzy.do/6181285/cards/107)
  (shared preference permissions), [108](https://app.fizzy.do/6181285/cards/108)
  (failed tool-result semantics), and
  [109](https://app.fizzy.do/6181285/cards/109) (SST turn commitment). Existing
  cards [94](https://app.fizzy.do/6181285/cards/94),
  [95](https://app.fizzy.do/6181285/cards/95),
  [97](https://app.fizzy.do/6181285/cards/97), and
  [103](https://app.fizzy.do/6181285/cards/103) contain the related regression,
  lifecycle, recap, and language context.
- `assets/bin/convert` now only enforces the 8 kHz mono PCM format. The separate
  `assets/bin/normalize` command uses the absolute `-35.5 LUFS` target; the
  tracked cues measure about `-35.1` to `-35.3 LUFS` except for one short cue at
  `-33.7 LUFS`. Repeating normalization on a test copy did not compound the
  reduction.
```

#### GOOD

* Newlines and blank lines and lists optimized for human reading
* Informational niveau more appropriate for humans (LUFS details summarized)

```
- Call `4445` produced `Wie ist das weg?` and then the corrected  `Wie ist das Wetter in Berlin?`;
  call `6e14` repeated the same favorite color transcript.
  STT passes took about 2.4–3.7 seconds, and the caller disconnected while the provider was still working.

- The findings are tracked in Fizzy
  - [107](https://app.fizzy.do/6181285/cards/107) (shared preference permissions)
  - [108](https://app.fizzy.do/6181285/cards/108) (failed tool-result semantics)
  - [109](https://app.fizzy.do/6181285/cards/109) (SST turn commitment).

  Related existing cards:
  - [94](https://app.fizzy.do/6181285/cards/94)
  - [95](https://app.fizzy.do/6181285/cards/95)
  - [97](https://app.fizzy.do/6181285/cards/97)
  - [103](https://app.fizzy.do/6181285/cards/103)

- Split up the responsibilities of `assets/bin/convert` `assets/bin/normalize`:
  - `convert` now only enforces the 8 kHz mono PCM format
  - `normalize` sets the volume to an absolute value (LUFS)
  Measures show the files are now at that absolute volume level.
  Both scripts are idempodent.
```
