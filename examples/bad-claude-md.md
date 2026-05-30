# Bad CLAUDE.md: A Novice Anti-Pattern

[中文版本](bad-claude-md-zh.md)

This is the kind of CLAUDE.md a developer writes on their first attempt —
before they understand what makes a behavioral constraint actually work.
Every line is either too vague to act on, too vague to verify, or not a
standing rule at all.

---

## The Novice Version

```
# CLAUDE.md

Write good code.
Don't make mistakes.
Use best practices.
Follow the project conventions.
Be helpful and professional.
今天帮我写登录页面。
Write tests for everything.
Keep the code clean.
Always use the latest version of dependencies.
Make it fast.
```

Ten lines. Every line is broken.

---

## Line-by-Line Analysis

### ❌ "Write good code"

**Why it fails:** "Good" has no operational definition. Two developers (let
alone two LLM sessions) will interpret it differently every time. It cannot
be verified and it cannot be enforced.

**Maps to Rule 4 (Goal-Driven Execution).** A rule must state *how* to
achieve the goal, not just the aspiration. Correct form: "Define verifiable
success criteria for every task. Loop until they pass."

**Also maps to Rule 8 (Generated Artifact Self-Check).** "Good" fails the
falsifiability test. Correct form: "Every artifact ships with a structured
checklist of yes/no items. 'Looks good' is not an item."

---

### ❌ "Don't make mistakes"

**Why it fails:** It names the negative outcome ("mistakes") but provides
zero mechanism for preventing it. It is the equivalent of telling a driver
"don't crash" without mentioning mirrors, signals, or speed limits.

**Maps to Rule 1 (Think Before Coding).** The rule that actually prevents
mistakes is: "State your assumptions explicitly. If uncertain, ask. If
something is unclear, stop and name what's confusing."

---

### ❌ "Use best practices"

**Why it fails:** "Best practices" is a rhetorical shortcut that means
"whatever I feel like today." It signals nothing concrete. Worse, it
invites over-engineering — abstractions, design patterns, and configurability
that nobody asked for.

**Maps to Rule 2 (Simplicity First).** The correct directive is: "Minimum
code that solves the problem. No features beyond what was asked. No
abstractions for single-use code." That is specific and falsifiable.

---

### ❌ "Follow the project conventions"

**Why it fails:** This presupposes conventions exist and names none of them.
If the conventions are in a separate file, link to it. If they are not
written down, there is nothing to follow. The instruction wastes a line
without adding any constraint.

**Maps to Rule 3 (Surgical Changes).** A useful convention rule says
exactly what to match: "Match existing indentation, naming, and import
style. Touch only what you must. Every changed line should trace directly
to the user's request."

---

### ❌ "Be helpful and professional"

**Why it fails:** This is a value statement, not an instruction. An LLM
cannot execute "be professional" — it needs concrete behaviors: tone rules,
response structure, when to push back, when to ask clarifying questions.

**Maps to Rule 1 (Think Before Coding).** The behavioral equivalent is:
"If a simpler approach exists, say so. Push back when warranted. If
multiple interpretations exist, present them — don't pick silently."

---

### ❌ "今天帮我写登录页面"

**Why it fails:** This is a task prompt, not a standing rule. CLAUDE.md is
read at the start of every session. A specific task like "write the login
page today" is meaningless on day two, confusing on day three, and actively
harmful when it causes Claude to re-execute a task that is already done.

**Maps to the Design Philosophy (README).** CLAUDE.md is "a set of standing
orders — rules that apply across every task, not instructions for a specific
task." Tasks belong in the chat prompt. Rules belong in CLAUDE.md. Never
mix them.

---

### ❌ "Write tests for everything"

**Why it fails:** "Everything" is unbounded and impractical. Should Claude
write tests for a one-line config change? For a README edit? This turns a
reasonable goal into noise. Without specifying *what kind* of test, the LLM
may write superficial tests that pass trivially and catch nothing.

**Maps to Rule 4 (Goal-Driven Execution).** The correct directive ties
testing to specific success criteria: "Write a test that reproduces the
bug, then make it pass." The test is a tool for verification, not an end
in itself.

---

### ❌ "Keep the code clean"

**Why it fails:** Same pattern as "Write good code." "Clean" is subjective
and unverifiable. One person's clean is another person's over-abstracted.
The word carries emotion but zero precision.

**Maps to Rule 2 (Simplicity First).** Replace with: "If you write 200
lines and it could be 50, rewrite it. Would a senior engineer say this is
overcomplicated?" That gives a concrete heuristic instead of a vague
adjective.

---

### ❌ "Always use the latest version of dependencies"

**Why it fails:** Blindly upgrading dependencies breaks things. This rule
conflicts with itself — "don't make mistakes" on line two, but "always
upgrade everything" on line nine. A standing rule should not mandate
destructive actions.

**Maps to Rule 7 (Cross-Reference Discipline).** When dependencies change,
you must check every file that references them: "Every change has a blast
radius. Before you mark a task done, audit every file affected by it."
The correct behavior is *audit the blast radius*, not *always upgrade*.

---

### ❌ "Make it fast"

**Why it fails:** No target. No metric. No way to know when you are done.
"Fast" could mean 10ms for a CLI tool or 100ms for a web request — the LLM
has no way to choose.

**Maps to Rule 4 (Goal-Driven Execution).** Performance is a success
criterion like any other. The correct form provides a target: "The endpoint
must respond in under 200ms p95." If you cannot quantify the goal, you
cannot verify it.

---

## The Structural Problem

Beyond individual lines, the novice CLAUDE.md has no architecture:

- **No grouping.** All ten lines are flat. There is no reason this is ten
  items instead of one or fifty. A well-structured CLAUDE.md groups related
  rules under numbered sections so the LLM can navigate them predictably.
- **No priority.** "Write good code" and "Don't make mistakes" mean roughly
  the same thing, repeated twice. When everything is equally important,
  nothing is important. The nine rules in this repo are ordered: Rule 1
  (thinking before acting) must come before Rule 4 (verifying results).
- **No tradeoff acknowledgment.** The novice file presents an impossible
  ideal — all rules, all the time. The real CLAUDE.md opens with "These
  guidelines bias toward caution over speed. For trivial tasks, use
  judgment." That single sentence prevents the LLM from applying surgical
  change rules to a one-word typo fix.

---

## What the Novice File Gets Right (Accidentally)

- It exists. A bad CLAUDE.md is infinitely better than no CLAUDE.md,
  because it gives you something to improve. Every single line in this
  analysis came from a real file a real developer wrote, realized was
  broken, and then replaced.
- It is short. Ten lines. The instinct to be brief is correct — the nine
  rules in this repo fit on one screen. The mistake was filling those ten
  lines with aspirations instead of instructions.

---

## Key Takeaway

Every line in your CLAUDE.md must answer three questions. If a line cannot
answer all three, delete it or rewrite it until it can. First: **What
specific behavior should Claude perform?** Not "be helpful" — that is a
value. "State your assumptions explicitly before writing code" is a
behavior. Second: **How would you verify compliance?** Not "write good
code" — you cannot check that. "Every generated artifact ships with a
checklist of yes/no items" is checkable. Third: **Does this apply to
every task, or is it a one-time instruction?** If it is a one-time
instruction ("write the login page"), it does not belong in CLAUDE.md. If
it is a standing constraint ("match existing style in every edit"), it
does. The difference between a novice CLAUDE.md and a professional one is
not length, format, or cleverness. It is whether every line survives this
three-part test. The version at the top of this page had ten lines and
zero of them survived. Go check your own CLAUDE.md against this test. You
will probably find at least one line that needs rewriting.
