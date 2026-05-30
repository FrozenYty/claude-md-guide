# 2. Simplicity First

[中文版本](02-simplicity-first-zh.md)

## Why this rule exists

LLMs have an instinct for over-engineering. Given a straightforward task,
they reach for configuration flags, plugin architectures, and extension
points — not because the problem demands it, but because training data is
full of production-scale code that does. The model pattern-matches to the
most sophisticated version of "code that solves X," not the simplest one.

This tendency is especially costly because LLM-generated abstractions are
often wrong abstractions. They anticipate extension directions that never
materialize and add indirection without adding value. A single-function
feature wrapped in a class with a strategy pattern is not cleaner — it is
harder to read, harder to change, and harder to delete.

A direct case: the `polish-abstract` prompt. The request was "polish this
academic abstract." The agent could have added a journal parameter, a word
count flag, a discipline-specific terminology block. Instead, it kept a
flat 5-paragraph structure with zero extra parameters. Nothing to
misconfigure. Similarly, the writing-anti-patterns reference documents
only 12 entries — the most fatal, high-frequency mistakes — rather than
chasing exhaustiveness.

## What it looks like in practice

**Over-engineered:** User asks for a script renaming `.jpeg` to `.jpg`.
Agent writes 120 lines with `--dry-run`, `--recursive`,
`--preserve-timestamps`, a config parser, and a logging module. User now
has to read 120 lines to trust a 10-line operation.

**Simple:**
```python
import os, sys
folder = sys.argv[1]
for f in os.listdir(folder):
    if f.endswith('.jpeg'):
        os.rename(os.path.join(folder, f),
                  os.path.join(folder, f[:-5] + '.jpg'))
```
Eight lines. No flags. No abstractions. Readable in five seconds.

## When to relax it

If building infrastructure called from three or more distinct places,
some upfront abstraction is warranted. If the user explicitly asks for
configurability, add it — but only what was asked.
