# 7. Cross-Reference Discipline

[中文版本](07-cross-reference-discipline-zh.md)

## Why this rule exists

LLMs treat each task as a point change: edit one file, declare success, move on. But software projects are graphs of references. When you rename a file, move a function, update a config value, add a section, or delete a component — every file that points to the old location is now a bug. The LLM's natural instinct is to stop at the primary edit. The rule forces it to execute the secondary edits before declaring done.

Numbers, section IDs, file names, and configuration values are the most copy-pasted strings in any project. They appear in index tables, READMEs, CHANGELOGs, eval descriptions, routing tables, and inline comments. When you change the source, every copy silently drifts — and these are the hardest bugs to notice in review because the text looks perfectly plausible in isolation. There is no red squiggly line under a wrong section number.

A concrete case: `drawio-templates.md` was restructured — four templates were removed, eleven were added. The actual templates moved to new sections. But the index tables at the top of the file were never updated. **Ten out of ten** index entries pointed to wrong sections. A user following "Flowchart → §3" would land on the RAG pipeline template.

This error then propagated to `examples/README.md`, `evals/evals.json`, and `CHANGELOG.md`. Three independent audit agents discovered different fragments of the same bug — none realized it was systemic.

Another case: a developer renamed a config field from `max_retries` to `retry_limit` in `config.py`. The LLM updated the definition but left 14 references to `max_retries` across seven files — test fixtures, CLI argument parsers, environment variable mappers, and a README example. All 14 became runtime errors. The fix was 30 seconds of grep, but the agent never ran it.

## What it looks like in practice

**After every change with cross-file impact, audit the blast radius:**

```bash
grep -r "old_filename" .          # file renames
grep -r "old_function_name" .     # function moves/renames
grep -r "old_config_key" .        # config changes
grep -r "§5" .                    # section number changes
grep -r "19 templates" .          # count changes
```

Fix every hit in the same commit. If the old reference was wrong in multiple files, it is one bug, not N separate commits.

## When to relax it

If the changed value is unique to one file and appears nowhere else (e.g., an internal variable name in a single script), the search step is ceremonial. The rule activates when a value has been documented, indexed, referenced in another file, or exposed as an interface — which is almost always true for file names, function signatures, config keys, counts, and section IDs.
