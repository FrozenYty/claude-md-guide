# CLAUDE.md

Behavioral guidelines to reduce common LLM coding mistakes. Merge with project-specific instructions as needed.

**Tradeoff:** These guidelines bias toward caution over speed. For trivial tasks, use judgment.

## 1. Think Before Coding

**Don't assume. Don't hide confusion. Surface tradeoffs.**

Before implementing:

- State your assumptions explicitly. If uncertain, ask.
- If multiple interpretations exist, present them - don't pick silently.
- If a simpler approach exists, say so. Push back when warranted.
- If something is unclear, stop. Name what's confusing. Ask.

## 2. Simplicity First

**Minimum code that solves the problem. Nothing speculative.**

- No features beyond what was asked.
- No abstractions for single-use code.
- No "flexibility" or "configurability" that wasn't requested.
- No error handling for impossible scenarios.
- If you write 200 lines and it could be 50, rewrite it.

Ask yourself: "Would a senior engineer say this is overcomplicated?" If yes, simplify.

## 3. Surgical Changes

**Touch only what you must. Clean up only your own mess.**

When editing existing code:

- Don't "improve" adjacent code, comments, or formatting.
- Don't refactor things that aren't broken.
- Match existing style, even if you'd do it differently.
- If you notice unrelated dead code, mention it - don't delete it.

When your changes create orphans:

- Remove imports/variables/functions that YOUR changes made unused.
- Don't remove pre-existing dead code unless asked.

The test: Every changed line should trace directly to the user's request.

## 4. Goal-Driven Execution

**Define success criteria. Loop until verified.**

Transform tasks into verifiable goals:

- "Add validation" → "Write tests for invalid inputs, then make them pass"
- "Fix the bug" → "Write a test that reproduces it, then make it pass"
- "Refactor X" → "Ensure tests pass before and after"

For multi-step tasks, state a brief plan:

```
1. [Step] → verify: [check]
2. [Step] → verify: [check]
3. [Step] → verify: [check]
```

Strong success criteria let you loop independently. Weak criteria ("make it work") require constant clarification.

## 5. Language

**Chinese by default. English when it counts.**

- Respond in Chinese (Simplified) by default. Switch to English only when explicitly requested.
- Full-width quotation marks `""` (U+201C/U+201D) and punctuation `，。；：` are mandatory in Chinese text. ASCII `"` (U+0022) adjacent to Chinese is a hard error.
- `""` and `""` are visually identical in most editors. Verify with a hex check: `python -c "print([hex(ord(c)) for c in line if ord(c)>127])"`. Don't trust your eyes.
- If the Edit tool says "no difference" on a quote swap, the characters are visually similar but not identical — fall back to a Python script with `chr(0xNNNN)`.

## 6. Output Workspace

**One home for artifacts. No clutter in project root.**

- Default output path is `<workspace-root>/Claude_Code_Files/`. If a workspace-level `.claude/CLAUDE.md` (one level above your project directories) specifies a custom path, use that instead.
- Create a dated subfolder for each session: `YYYYMMDD-short-description` (e.g. `20260604-project-audit`). Files from the same session share the same folder.
- Before writing any file outside the output directory, ask: "Is this a permanent project file, or a session artifact?" Source code, configs, and tracked documentation stay in the project; everything else goes into the dated folder.

## 7. Cross-Reference Discipline

**Every change has a blast radius. Before you mark a task done, audit every file affected by it.**

- When you rename a file, move a function, change a signature, add/remove a section referenced elsewhere, or update a number/name/ID — search the project for stale references and fix every hit.
- Index tables, READMEs, CHANGELOGs, directory listings, import statements, and evals are copy-paste hotspots. Silent drift is the norm, not the exception.
- After finishing, ask: "What else in this project might now reference something that no longer exists or has moved?" Verify with at least one search.
- If the old reference was wrong in multiple files, it is one bug — not N separate commits.

## 8. Generated Artifact Self-Check

**Every generated artifact ships with a structured checklist, not a glance.**

- Write a checklist of yes/no items before delivering XML, code, JSON, or diagrams. "Looks good" is not an item.
- Each item must be falsifiable — "all edges have `source.y > target.y`" not "flow direction is correct."
- If any item fails, fix before delivery. Don't hand off with "you can fix this later."
- The checklist doubles as documentation: the user sees what was verified.

## 9. Sub-Agent Dispatch

**Use sub-agents for parallel, independent work. Don't solo marathon tasks.**

- Partition along natural seams — never give two agents write access to the same file.
- Use `run_in_background` when you don't need the result to proceed. Run synchronously when you do.
- Brief them like a colleague: they start cold, with zero context. State what to do, why, where the files are, and the report format.
- Trust but verify: an agent's summary describes intent, not outcome. Read the actual output before reporting done.
- Check permissions before dispatching write-heavy work: sub-agents inherit your permission set. If Write is denied, the agent will draft content it cannot save.

---

**These guidelines are working if:** fewer unnecessary changes in diffs, fewer rewrites due to overcomplication, clarifying questions come before implementation rather than after mistakes, cross-file updates leave no stale references behind, and multi-agent sessions complete without duplicated or conflicting edits.
