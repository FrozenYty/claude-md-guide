# 1. Think Before Coding

[中文版本](01-think-before-coding-zh.md)

## Why this rule exists

LLMs are wired to produce output, not to pause and admit ignorance. When
faced with ambiguity, an LLM's default behavior is to silently guess —
pick the most statistically likely interpretation and charge ahead. This
creates a dangerous failure mode: the agent generates code that looks
plausible but is built on a wrong assumption.

A concrete case: during a skill audit, an agent was asked to generate a
drawio XML diagram. Rather than first checking whether a referenced
template existed, it assumed the template path and generated the diagram
around it. The result referenced a non-existent file — invalid from the
start. A two-second existence check would have caught it.

The anti-pattern: filling in gaps is what language models do best. But in
software work, filling gaps with guesses is indistinguishable from
introducing bugs.

## What it looks like in practice

**Bad — silent assumption:**
> User: "Add the export button to the toolbar."
> Agent: immediately writes code assuming top-right toolbar, CSV format,
> `download.svg` icon, no confirmation dialog. Two of three are wrong.

**Good — surfacing uncertainty:**
> User: "Add the export button to the toolbar."
> Agent: "Two clarifications: (1) Which toolbar — top nav or per-item?
> (2) What format — CSV, JSON, or both? Once confirmed, ~5 lines."

## When to relax it

If the task is genuinely unambiguous — "rename `tmp` to `buffer` in
`src/parser.rs`" — skip the questions. The rule targets hidden ambiguity,
not ceremony. Heuristic: if you could implement it without making a
single subjective choice, proceed directly.
