# 3. Surgical Changes

## Why this rule exists

LLMs edit files the way a well-meaning houseguest tidies your apartment:
they rearrange things that were fine where they were. Asked to change one
line, an agent will often "improve" nearby formatting, rewrite a comment
it considers unclear, or refactor a helper function in the same file —
none of which was requested. These drive-by cleanups compound into noisy
diffs where the actual change is buried under cosmetic edits.

In code review, this is catastrophic. A reviewer opens a diff expecting a
one-line logic fix and faces 40 changed lines across three files — the
fix, plus reformatted whitespace, plus renamed variables, plus a
"cleaned up" import block. The reviewer has two bad options: spend time
separating signal from noise, or trust the diff and miss bugs.

A crisp example: while editing a SKILL.md frontmatter block to update
the `description` field, the agent left every other field — the routing
table, the tool definitions, the version string — completely untouched.
The diff was exactly one line. A reviewer sees it instantly.

## What it looks like in practice

**Bad — scope creep:** Asked to change one CSS color, the agent also
reorders properties alphabetically, converts `px` to `rem` in adjacent
rules, and deletes a commented-out block "since it's dead code." The diff
is 60 lines. Which part is the bug fix? Nobody knows at a glance.

**Good — the minimal edit:** The agent changes exactly the `background:
#f00` line to `background: #0f0` and nothing else. One-line diff. If it
noticed dead code, it says so in the response — "by the way, lines 45-48
appear unreachable" — without touching them.

## When to relax it

If your change introduces a new function that builds on a poorly
structured existing API, and using the API as-is would make your new code
worse, refactoring that API is in scope — it is now your dependency, not
someone else's code you happen to dislike. You are responsible for the
code you write, including its integration points. You are not responsible
for polishing code you merely happened to scroll past.
