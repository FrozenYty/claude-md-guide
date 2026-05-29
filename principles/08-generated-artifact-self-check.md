# 8. Generated Artifact Self-Check

## Why this rule exists

An agent generating XML, code, or JSON will confidently deliver output
that "looks right" but fails under structural scrutiny — unclosed tags,
missing required attributes, wrong flow directions, unescaped characters.
These are not subtle bugs; they are checkable in ten seconds. But without
an explicit checklist, the agent's default behavior is to glance at the
output and declare it done.

A concrete case: the drawio diagram generator uses a 13-item self-check
before writing any XML. Item 12: "Every forward edge satisfies `source.y
> target.y`." Without this item, inverted stacks — the most common drawio
bug — would pass visual review every time. The check makes an invisible
coordinate property visible.

Similarly, the plotting code generator uses a 10-item self-check. Item 3:
"Is `pdf.fonttype = 42` set?" This one line is the difference between a
paper passing ACM/IEEE submission and being rejected at the PDF-check
stage. It takes one second to verify — but only if it is on a list.

## What it looks like in practice

**Bad — "looks good":**
> Agent: "Here's your diagram. It looks correct." Ships an XML file with
> two overlapping shapes, an edge pointing to a deleted node, and a
> hardcoded color that clashes with the IEEE palette. All three bugs
> would have been caught by a checklist.

**Good — structured verification:**
```
Self-check:
1. XML well-formed? pass
2. All edges ref existing nodes? pass
3. All forward edges source.y > target.y? pass
4. No out-of-page elements? pass
...
13. No double-escaped &amp;amp;? pass
```
Each item is a yes/no question. The user sees what was verified.

## When to relax it

For throwaway artifacts (a one-off data dump, a debug log), the
checklist is unnecessary. For anything the user will use directly — a
config file, a generated script, a diagram, an eval — the checklist is
non-negotiable.
