# 6. Output Workspace

## Why this rule exists

LLM coding sessions produce artifacts: temporary scripts, generated
reports, screenshots, log files. Without a designated home, these
accumulate in the project root — alongside source files, configs, and
build outputs — making it impossible to tell what is permanent and what
is disposable.

The rule enforces two disciplines: (1) all artifacts go into
`Claude_Code_Files/`, never the project root, and (2) every session gets
a dated subfolder. The date prefix (`YYYYMMDD-`) means folders sort
chronologically; the description suffix makes them findable.

A concrete case: today's session produced three temporary Python scripts
(`check_quotes.py`, `fix_quotes.py`, `fix_savefig.py`) and a skill
repository. They went into `20260530-skill-review/` (temporary) and
`20260530-academic-writing-toolkit-dev/` (project), respectively. Not a
single file landed in the project root.

## What it looks like in practice

**Bad — clutter in root:**
```
myproject/
├── fix.py
├── fix2.py
├── check_output.py
├── report.md
├── screenshot.png
├── src/
├── ...
```
Which files are project source and which are session artifacts? No way to
know without reading them.

**Good — dated, described folders:**
```
myproject/
├── Claude_Code_Files/
│   ├── 20260521-legado-espresso/
│   │   ├── test-report.md
│   │   └── screenshots/
│   └── 20260530-skill-review/
│       ├── check_quotes.py
│       └── fix_quotes.py
├── src/
├── ...
```
Permanent and temporary are cleanly separated.

## When to relax it

For trivial, single-use commands that don't produce files (e.g., `git
status`), the rule imposes no overhead. The rule activates when you are
about to write a file to disk — ask: does this belong in the project
permanently, or is it a session artifact?
