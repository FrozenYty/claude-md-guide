# 4. Goal-Driven Execution

[中文版本](04-goal-driven-execution-zh.md)

## Why this rule exists

Vague success criteria produce two symmetric failures: the agent stops too
early (delivering something that "looks done" but fails under scrutiny) or
never stops at all (endlessly polishing because no definition of "good
enough" exists). Both trace to the same root — the task was never phrased
as a falsifiable condition.

"Make the output look professional" is not a success criterion. It cannot
be verified. An agent cannot loop on it because "professional" has no
binary answer. The result is either a premature handoff with formatting
errors, or an infinite cycle of margin adjustments.

A concrete case: the Chinese typography self-check. The criterion was not
"make sure quotes look right" — unverifiable. It was "zero ASCII
double-quote characters (U+0022) adjacent to Chinese text." Falsifiable.
After every edit, running `check_quotes.py` and seeing `0 issues` is a
binary pass/fail. The agent can loop independently: edit, verify, edit,
verify — until the script returns zero. No human judgment needed.

## What it looks like in practice

**Vague:** Task: "Refactor the auth module." Success: "the code should be
cleaner." Agent restructures functions, renames variables, declares
victory. Three weeks later: token refresh is broken — but "cleaner" was
met, so the agent considered it done.

**Falsifiable:** Task: "Refactor the auth module." Plan: (1) Extract token
logic into `token.rs` — verify: `cargo test auth::` passes. (2) Replace
ad-hoc error strings with `AuthError` enum — verify: `cargo build` has
zero warnings. (3) Remove legacy functions — verify: `grep -r legacy_
src/` returns nothing. Each step has a binary check.

## When to relax it

Exploratory tasks ("investigate why the login page is slow") lack binary
criteria upfront. The deliverable is a report, not a passing test suite.
Shift the criterion from "does it pass?" to "did I produce a specific,
actionable finding?" — "it might be the database" is not done; "`GET
/session` takes 2.3s because of `bcrypt.verify` on every page load" is.
