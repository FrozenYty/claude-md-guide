# Case Study: The Section-Number Drift Bug

## Which rules were involved

- **Rule 7 (Cross-Reference Discipline)** — the primary rule that would have
  prevented the bug.
- **Rule 3 (Surgical Changes)** — the refactor that caused the drift touched
  templates but not the index tables that referenced them.

## What happened

The `academic-writing-toolkit` skill went through a template restructuring:
four weak architecture templates were removed, and eleven general-layout and
classic-diagram templates were added. The actual templates lived at sections
§1 through §15 in `drawio-templates.md`.

But the **index tables** at the top of the file were never updated. They
still pointed to the old section numbers — claiming that "Flowchart" was at
§3 when it was actually at §10, "State Machine" was at §3 when it was at
§14, and so on. **Ten out of ten** index entries were wrong.

This error then propagated silently to:
- `examples/README.md` — claimed Diffusion was at §5 (actually §2)
- `evals/evals.json` — same §5→§2 error in expected-output descriptions
- `CHANGELOG.md` — claimed 19 total templates (actually 15), and 8
  architecture templates (actually 4, the other 4 were removed)

Three independent audit agents discovered different fragments of the same
bug across different files — each found one copy, none realized it was a
systemic drift.

## What went wrong

The root cause was a **one-directional refactor**. The author changed the
source files (the template content) but never searched for references to
the old numbers. Counts, section numbers, and file references had been
copy-pasted into index tables, READMEs, CHANGELOGs, and eval descriptions
during earlier commits. When the source changed, every copy silently
drifted.

Numbers are the highest-risk text in documentation. A typo in a sentence
is obvious. A wrong section number in an index table is invisible — the
reader has no way to know it's wrong until they follow the link and land
on the wrong page.

## How the rules would have prevented it

If Rule 7 had been in place, after changing the template structure, the
author would have searched the project for `§1`, `§2`, `8 templates`, `19
templates`, and every other number that changed. The cross-reference
search would have turned up all three downstream files in seconds.

Rule 3 would have added guardrails: "touch only what you must" means
knowing the blast radius of a change. A template restructuring that doesn't
update the index is a change that's not done.

## Key takeaway

**After changing a number, search for it.** Counts and section IDs are
copy-pasted more than any other string in a project. A 30-second grep
saves three files of silent drift.
