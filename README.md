# Slice-Driven Dev

A Claude Code skill that enforces disciplined software engineering habits when working with AI — so you spend less time untangling messes and more time shipping.

## What it does

When you load this skill, Claude follows a set of protocols that kick in automatically at the right moments:

**Clarify before coding.** If the intent isn't clear, Claude asks one targeted question at a time — not a wall of clarifying questions — until it's confident enough to act. It won't start building based on a guess.

**Work in tiny, verifiable slices.** Each piece of work is declared upfront: what it does, what it explicitly does *not* do, and how you'll know it's done. No mixing features with refactors, no "while I'm at it" scope creep.

**Keep tests green.** Changed a path? Run the tests. Added a new branch? Write a test for it. Fixed a bug? Write the failing test first. If there's no test framework, Claude tells you what it manually verified instead of pretending everything is fine.

**AI generates inputs; deterministic tools judge outputs.** Claude proposes, a compiler/test runner/linter decides. "I checked and it looks fine" without running a tool doesn't count.

**Document continuously, not at the end.** Every slice produces three artifacts as a side effect: a code trail entry (file:symbol → what changed), a decision record (when something was chosen over alternatives), and an architecture update (when module boundaries move). The decision template is kept intentionally tight — if it can't fit the decision in 30 words, the decision isn't clear yet.

**Break large tasks into slices before starting.** Anything that spans multiple files or multiple steps gets decomposed into independent, committable units first. Each unit depends only on file state, not on "what we discussed earlier in the conversation."

**Refresh the session checkpoint before switching context.** When a large slice is done or a session is about to end, Claude overwrites `SESSION_CHECKPOINT.md` with the minimum state a fresh session needs to pick up: what's in progress, the next concrete actions, and what's uncommitted. The next session reads this file first.

## Who it's for

Anyone using Claude Code for non-trivial engineering work — especially when tasks span multiple sessions, multiple files, or involve decisions that need to be traceable later.

## Usage

Place `SKILL.md` in your Claude Code skills directory (`~/.claude/skills/software-engineering/`). The skill activates automatically on any coding, refactoring, or documentation task. Specific protocols are triggered by keywords:

| Say... | Triggers |
|---|---|
| "refactor" | Confirm tests are green before touching structure |
| "make a slice" / "do this in one pass" | Declare scope, exclusions, and done-criteria |
| "break this down" / long multi-step task | Decompose into independent slices with TaskCreate |
| "document this" / "log the decision" | Decision template (30-word rule enforced) |
| "how do we test this" | AI proposes cases; deterministic tool gives the verdict |
| "refresh checkpoint" / end of session | Overwrite SESSION_CHECKPOINT.md for hot-start |

## Core principle

A slice is the unit of work. It has a declaration, a test, a code-trail entry, and a commit. Everything else is noise.
