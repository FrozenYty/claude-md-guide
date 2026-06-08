# 0. Three-Tier CLAUDE.md System

[中文版本](00-three-tier-system-zh.md)

## Why this matters

Most projects start with a single `CLAUDE.md` in the project root. That
works until you have multiple projects, or until you realize some rules
apply everywhere (coding style, language) while others are specific to
one project (build commands, architecture).

The three-tier system separates concerns so that universal rules are
written once and project-specific rules stay lean.

## The three tiers

| Tier | Location | Scope | Example |
|------|----------|-------|---------|
| **User-level** | `~/.claude/CLAUDE.md` | All projects, all workspaces | Coding style, language preference, behavioral rules |
| **Workspace-level** | `<workspace>/.claude/CLAUDE.md` | All projects under this workspace | Override output path, shared conventions |
| **Project-level** | `<project>/.claude/CLAUDE.md` | This project only | Build commands, architecture, project-specific conventions |

## How they interact

Claude Code loads all applicable CLAUDE.md files and merges them. The
rule of thumb:

- **User-level** provides defaults — universal behavioral guidelines that
  apply everywhere.
- **Workspace-level** overrides or extends user-level rules where the
  workspace has different needs. For example, a multi-project workspace
  may fix the output path to a shared `Claude_Code_Files/` directory
  rather than one per project.
- **Project-level** adds project-specific instructions — tech stack,
  build commands, architecture notes.

When a rule exists at multiple levels, the more specific level wins:
project overrides workspace, workspace overrides user.

## Decision framework

When you need to add a new rule, ask:

```
Does this apply to ALL projects?
├── YES → Put it in user-level ~/.claude/CLAUDE.md
└── NO → Does it apply to ALL projects under this workspace?
    ├── YES → Put it in workspace-level <workspace>/.claude/CLAUDE.md
    └── NO → Put it in project-level <project>/.claude/CLAUDE.md
```

## Real example: Rule 6 (Output Workspace)

**Before** — Rule 6 lived in a project-level CLAUDE.md:

```markdown
## 6. Output Workspace
- `Claude_Code_Files/` is the dedicated directory...
```

This worked for one project but caused confusion: each project created
its own `Claude_Code_Files/` folder, and the user actually wanted all
artifacts in `D:\MYPROJS\Claude_Code_Files\`.

**After** — Rule 6 was promoted to user-level with a delegation clause:

User-level `~/.claude/CLAUDE.md`:
```markdown
## 6. Output Workspace
- Default output path is `<workspace-root>/Claude_Code_Files/`.
  If a workspace-level `.claude/CLAUDE.md` specifies a custom path,
  use that instead.
- Create a dated subfolder for each session: `YYYYMMDD-short-description`.
```

Workspace-level `D:\MYPROJS\.claude\CLAUDE.md`:
```markdown
## Output Workspace (Override Rule 6)
The output directory is fixed to: D:\MYPROJS\Claude_Code_Files\
```

**Result**: All nine behavioral rules now live in the user-level file
(shared across all workspaces). The workspace-level file is 18 lines
with one override. Every project under `D:\MYPROJS` writes artifacts to
the same shared directory.

## Relationship with .claude/rules/ and auto memory

The three-tier CLAUDE.md system is one part of a larger instruction
architecture:

| Component | Who writes | Granularity | Best for |
|-----------|-----------|-------------|----------|
| User-level CLAUDE.md | You | All projects | Universal behavioral rules |
| Workspace-level CLAUDE.md | You | All projects under a workspace | Shared path/config overrides |
| Project-level CLAUDE.md | You | One project | Build commands, architecture |
| `.claude/rules/*.md` | You | Path-scoped within a project | Language-specific or directory-specific rules |
| Auto memory | Claude | Accumulated per project | Build commands, debugging insights, preferences |

## Migrating from a single CLAUDE.md

If you have one large project-level CLAUDE.md, here is the migration
path:

1. **Identify universal rules.** Rules about coding style, simplicity,
   language, and general behavior belong at the user level.
2. **Identify workspace overrides.** If your workspace has a custom output
   path, shared conventions, or global config, put them at the workspace
   level.
3. **Keep project-specific rules.** Build commands, tech stack details,
   and architecture notes stay at the project level.
4. **Split path-scoped rules.** Rules that only apply to `src/api/` or
   `*.test.ts` should move to `.claude/rules/` with `paths:` frontmatter.
5. **Verify.** Run `brain-admin diagnose` to check for conflicts, stale
   references, and size limits.
