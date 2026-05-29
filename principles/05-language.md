# 5. Language

## Why this rule exists

This rule has two layers: (a) default response language, and (b) the
Unicode typography discipline required for Chinese output.

The response-language layer exists because bilingual users waste time
translating when the agent guesses wrong. Defaulting to Chinese and
switching to English on request eliminates that friction.

The typography layer exists because LLMs routinely produce ASCII `"`
(U+0022) adjacent to Chinese text — the single most common formatting
error in Chinese LLM output. The full-width quotation marks `""`
(U+201C/U+201D) are mandatory in Chinese typography, but they are
visually indistinguishable from ASCII `"` in most monospace editors. You
cannot trust your eyes to catch this error.

A concrete case: during an audit of 24 prompt files, one file —
`humanize-zh.md`, the file that *defines the Chinese typography rules* —
had five distinct quote bugs. They included ASCII quotes in Chinese
context, a reversed quote pair (`U+201D...U+201C` instead of
`U+201C...U+201D`), and a left-quote used as a closing right-quote. A
human proofreader scanning the file would see no difference.

## What it looks like in practice

**Bad — invisible to the eye:**
```
carries an obvious "machine flavor" or "translationese" into natural academic prose
```
The quotes around "machine flavor" and "translationese" are ASCII `"`
(U+0022). They look correct but are typographically wrong in Chinese
context.

**Good — verified at byte level:**
```python
python -c "print([hex(ord(c)) for c in line if ord(c)>127])"
```
This one-liner reveals every non-ASCII character in the string. U+201C
and U+201D are correct; U+0022 is not.

## When to relax it

When the entire surrounding paragraph is in English (e.g., a code comment
or an all-English technical section), ASCII quotes are acceptable.
Similarly, when the output language is English-only, the Chinese
typography rules do not apply. The rule activates specifically when
Chinese characters appear in the output.
