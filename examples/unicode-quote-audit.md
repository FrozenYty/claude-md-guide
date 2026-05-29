# Case Study: The Unicode Quote Audit

## Which rules were involved

- **Rule 5 (Language)** — the Chinese typography sub-rule and Unicode
  verification sub-rule.
- **Rule 8 (Generated Artifact Self-Check)** — a structured checklist
  would have caught these before delivery.

## What happened

During a comprehensive audit of the `academic-writing-toolkit` skill's 24
prompt files, a Python script scanning for Unicode quote errors found five
distinct bugs in `humanize-zh.md` — the very file that *defines the
Chinese typography rules for the entire toolkit*.

The five bugs spanned three categories:

1. **ASCII quotes in Chinese context**: Line 4 used `"machine flavor"`
   and `"translationese"` with ASCII `"` (U+0022) — a direct violation of
   the toolkit's own Chinese typography rule, since the surrounding
   paragraph was not entirely in English.

2. **Reversed quote direction**: Line 14 wrapped `"de-AI"` as
   `U+201D...U+201C` (right-quote first, left-quote second). The quotes
   were full-width but the direction was backwards — a bug invisible to
   any human reader but detectable at the byte level.

3. **Left quote used as right quote**: Line 18 ended an example phrase
   with `"` (U+201C, left) instead of `"` (U+201D, right), creating a
   4:2 left-to-right imbalance on that line.

## What went wrong

Three compounding failures:

1. **Visual indistinguishability**: In most monospace editors, `""`
   (U+201C/U+201D) and `""` (U+0022) render identically. A human
   proofreader scanning the file would see no difference.

2. **Tool-level normalization**: The `Edit` tool silently rejected
   attempts to swap ASCII quotes for full-width quotes, reporting "no
   differences" — because the characters look the same to the tool's
   comparison logic.

3. **Dogfooding failure**: The file that teaches the toolkit how to use
   correct Chinese quotes was itself the most quote-buggy file in the
   repository. Nobody had run the verification script on the rules file.

## How the rules would have prevented it

Rule 5's Unicode verification sub-rule states: "Verify with
`python -c 'print([hex(ord(c)) for c in line if ord(c)>127])'`." Running
this one-liner on `humanize-zh.md` would have caught all five bugs in
under ten seconds.

Rule 8 would have required a self-check item like "All `"` adjacent to
Chinese text are U+201C/U+201D, not U+0022" — a falsifiable yes/no check.

## Key takeaway

**You cannot visually audit Unicode.** When correctness depends on
distinguishing `""` from `""` or `"` from `"`, use a hex-level script.
Trusting your eyes is how a typography-rules file ships with five quote
bugs.
