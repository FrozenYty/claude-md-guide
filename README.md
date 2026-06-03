# CLAUDE.md Guide

[中文版本](README-zh.md)

A practical guide to building and using `CLAUDE.md` — the behavioral
constraint file that shapes how Claude Code works in your project.

## What is CLAUDE.md?

`CLAUDE.md` is a plain Markdown file you place in your project root. Claude
Code reads it at the start of every session and follows its instructions.
Think of it as:

- A **linter for behavior** — it catches common LLM mistakes before they
  happen (over-engineering, speculative features, cross-file drift).
- A **shared mental model** — you and Claude agree on norms once, in one
  place, instead of repeating the same corrections every session.
- A **project onboarding doc** — new team members (human or AI) read it
  and know how to operate.

It is NOT a prompt in disguise. It is a set of standing orders — rules that
apply across every task, not instructions for a specific task.

## Why this exists

Most CLAUDE.md files are either empty, a single sentence, or a dump of
project-specific build commands. Few people treat them as a design object.

This guide argues that a well-crafted CLAUDE.md is the highest-leverage
investment you can make in a Claude Code project. The nine rules in this
repo are extracted from real sessions where the absence of each rule caused
actual bugs, wasted time, or produced unmaintainable output. Each rule is backed by concrete case studies in `examples/` and `principles/`.

## Quick start

1. Copy `CLAUDE.md` to your project root.
2. Remove rules that don't apply to your workflow.
3. Add one project-specific rule (e.g., your tech stack, your naming
   conventions).
4. Use it for a week. Notice what still goes wrong. Add a rule for it.
5. Repeat. A good CLAUDE.md is never finished, only converged.

## Structure of this repo

```
claude-md-guide/
├── CLAUDE.md            # The nine-rule template — copy this to your project
├── README.md            # You are here (中文版: README-zh.md)
├── principles/          # Deep dives on each rule (en + zh)
│   ├── 01-think-before-coding.md
│   ├── 02-simplicity-first.md
│   ├── 03-surgical-changes.md
│   ├── 04-goal-driven-execution.md
│   ├── 05-language.md
│   ├── 06-output-workspace.md
│   ├── 07-cross-reference-discipline.md
│   ├── 08-generated-artifact-self-check.md
│   ├── 09-sub-agent-dispatch.md
│   └── ...-zh.md        # Chinese translations of all nine
└── examples/            # Real case studies (en + zh)
    ├── bad-claude-md.md
    ├── section-number-drift.md
    ├── unicode-quote-audit.md
    └── ...-zh.md        # Chinese translations of all three
```

## The nine rules at a glance

| # | Rule | One-sentence summary |
|---|------|---------------------|
| 1 | Think Before Coding | State assumptions. Surface tradeoffs. Ask before guessing. |
| 2 | Simplicity First | Minimum code that solves the problem. No speculative features. |
| 3 | Surgical Changes | Touch only what you must. Match existing style. |
| 4 | Goal-Driven Execution | Define verifiable success criteria. Loop until they pass. |
| 5 | Language | Chinese by default. Full-width punctuation. Verify Unicode. |
| 6 | Output Workspace | Dated subfolders. No clutter in project root. |
| 7 | Cross-Reference Discipline | Audit the blast radius of every change. Fix all stale references. |
| 8 | Generated Artifact Self-Check | Every artifact ships with a structured checklist. |
| 9 | Sub-Agent Dispatch | Parallel independent work. Brief cold agents well. Verify output. |

## Design philosophy

Three principles shape every rule in this file:

1. **Prevention over correction.** A rule that stops a mistake before it
   happens is worth ten "please fix this" corrections after the fact.
   Each rule in this file traces back to a specific class of bug that
   recurred until the rule was written down.

2. **Verifiable, not aspirational.** "Write good code" is useless. "Every
   generated artifact ships with a structured checklist of yes/no items"
   is falsifiable. Every rule states what to DO, not what to VALUE.

3. **Minimal surface area.** Rules are not tutorials. They are short,
   imperative, and dense. If a rule needs more than three bullet points
   to explain, break it into two rules or move the explanation to
   `principles/`.

## Contributing

This is a living document. If you've discovered a rule that consistently
improves your Claude Code sessions, open an issue or PR.

- New rules need: a name, the one-sentence bold summary, 2-4 bullet
  points, and a real case study in `examples/` where the rule would have
  prevented a bug.
- Modified rules need: an updated case study showing the before/after.

