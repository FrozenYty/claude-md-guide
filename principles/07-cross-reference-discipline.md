# 7. Cross-Reference Discipline

[中文版本](07-cross-reference-discipline-zh.md)

## Why this rule exists

Numbers, section IDs, file names, and configuration values are the most
copy-pasted strings in any project. They appear in index tables, READMEs,
CHANGELOGs, eval descriptions, routing tables, and inline comments. When
you change the source, every copy silently drifts — and these are the
hardest bugs to notice in review because the text looks perfectly
plausible in isolation. There is no red squiggly line under a wrong
section number.

A concrete case: `drawio-templates.md` was restructured — four templates
were removed, eleven were added. The actual templates moved to new
sections (§1-§4 architecture, §5-§15 layouts/classic). But the index
tables at the top of the file were never updated. **Ten out of ten**
index entries pointed to wrong sections. A user following "Flowchart →
§3" would land on the RAG pipeline template.

This error then propagated to `examples/README.md` (Diffusion tagged as
§5, actually §2), `evals/evals.json` (same §5→§2 error), and
`CHANGELOG.md` (claimed 19 templates, actually 15; claimed 8 architecture
templates, actually 4). Three independent audit agents discovered
different fragments of the same bug — none realized it was systemic.

## What it looks like in practice

**Before changing a value that appears elsewhere:**
```bash
grep -r "§5" .          # find all references to old section number
grep -r "19 templates" . # find all references to old count
grep -r "8 architecture" . # find all references to old category count
```
Fix every hit. If the old value was wrong in multiple files, it is one
bug, not N separate commits.

## When to relax it

If the changed value is unique to one file and appears nowhere else
(e.g., an internal variable name in a single script), the search step is
ceremonial. The rule activates when a value has been documented, indexed,
or referenced in a different file — which is almost always true for
counts, section IDs, and file paths.
