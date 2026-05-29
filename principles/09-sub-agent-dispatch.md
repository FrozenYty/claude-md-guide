# 9. Sub-Agent Dispatch

## Why this rule exists

A single agent working sequentially on a large task will hit context
limits, lose focus across subtasks, and produce inconsistent output. The
same task partitioned across multiple sub-agents — each with a focused
scope and clear deliverable — finishes faster and with higher quality.
But partitioning incorrectly (overlapping scopes, insufficient context)
produces duplicated or conflicting edits that are worse than sequential
work.

A concrete case: auditing 33 files across prompts, references, examples,
and meta-documentation. A single agent would have taken 45+ minutes and
likely missed cross-file inconsistencies. Instead, three agents ran in
parallel: one on prompts (22 files), one on references + examples (10
files), one on meta-documentation (4 files). Each reported in under 5
minutes. Critically, all three independently discovered the same
section-number drift bug *in different files* — providing convergent
evidence that would have been missed by a single-agent sequential audit.

Another case: three agents regenerated examples (transformer.drawio,
diffusion.drawio, sota-comparison.py) simultaneously in the background
while the main session continued other work.

## What it looks like in practice

**Key principles for effective dispatch:**

1. **Partition along natural seams** — file directories, task categories,
   independent outputs. Never give two agents write access to the same
   file.

2. **Brief them like a colleague** — the agent starts cold, with zero
   context from your conversation. A good prompt states: what to do, why,
   where the files are, what format to report in, and a length cap.

3. **Background for independence, foreground for dependency** — use
   `run_in_background` when you don't need the result to proceed;
   synchronous when you do.

4. **Trust but verify** — an agent's summary describes what it *intended*
   to do. Read the actual changes before reporting the work as done.

**Bad dispatch:**
> "Review the skill and fix any issues." — Vague scope, no output format,
> agent might modify files the user hasn't approved.

**Good dispatch:**
> "Read all 22 files in `prompts/`. For each, check: (A) Self-Audit
> present, (B) no ASCII `"` near Chinese text, (C) placeholder naming
> consistent. Report grouped by A/B/C with line references. Report only
> — do not edit any files."

## When to relax it

For a task involving fewer than three files or a single clear action,
sub-agents add coordination overhead without benefit. The rule activates
when the task can be naturally partitioned and each partition is large
enough to justify a full agent context — roughly 5+ files or an
independent creative task.
