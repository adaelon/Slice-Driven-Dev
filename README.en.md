# Slice-Driven Dev

<img src="docs/images/Gemini_Generated_Image_y9hztuy9hztuy9hz.png" alt="Slice-Driven Dev" style="zoom: 20%;" />

**[中文版 →](README.md)**

Slice-Driven Dev is a Claude Code skill that enforces engineering discipline when AI writes code alongside you — making your project verifiable, traceable, and hand-off-ready.

AI writes code fast, but speed isn't the problem. The problem is touching too many things at once, starting without a clear intent, validating by feel instead of tools, and having no idea where to look when something breaks. This skill's core approach is **compressing every unit of work into a minimal "slice"** — each slice is declared upfront (what it does, what it doesn't, how you'll know it's done), and must ship with tests, a doc entry, and a commit. One slice, one thing. Every cut leaves a trail.

AI also has a structural limitation: **no memory across sessions**. Every new conversation starts from zero. This skill's answer is `SESSION_CHECKPOINT.md` — at the end of each major slice or session, Claude overwrites this file with the minimum a fresh session needs: what's in progress, the next concrete actions, what's uncommitted. The next session reads this file first and picks up with zero friction.

---

## What it does

Once loaded, Claude triggers the right protocol automatically at the right moment:

**Clarify intent before coding, never guess.** When a task is ambiguous, Claude asks one question at a time — the most direction-changing one — until it has ≥95% confidence. No building on assumptions that get torn down later.

**Work in tiny, verifiable slices.** Before touching anything, declare: what this slice does, what it explicitly excludes, and what "done" looks like. No mixing features with refactors. The word "while I'm at it" is a contamination signal — pull it out as a separate slice.

**Keep tests green.** Changed an existing path? Run the tests. Added a new branch? Write a test for it. Fixed a bug? Write the failing test first, then fix. If there's no test framework, Claude states what it manually verified instead of pretending coverage exists.

**AI generates inputs; deterministic tools judge outputs.** Claude proposes, writes code, lists edge cases. The compiler, test runner, and linter give the verdict. "I checked and it looks fine" without running a tool doesn't count.

**Documentation is a side effect of work, not a summary at the end.** Every slice produces three things in sync: a code trail entry (file:symbol → what changed), a decision record (what was chosen and what was rejected), and an architecture update (only when module boundaries move). The decision template has a hard constraint — 30 words max. If you can't compress it, the decision isn't clear yet.

**Break large tasks into slices before starting.** Anything spanning multiple files or multiple steps gets decomposed into independent, committable units first. Each unit depends only on file state, not on "what we discussed earlier in the conversation."

**Refresh the session checkpoint before switching context.** When a major slice is done or a session is about to end, Claude overwrites `SESSION_CHECKPOINT.md` with: current progress, next atomic actions, uncommitted work. The next session reads this file and continues without re-establishing context.

---

## Who it's for

Anyone using Claude Code for non-trivial engineering work — especially when tasks span multiple sessions or multiple files, or when decisions need to be traceable months later.

---

## How to use

Place `SKILL.md` in your Claude Code skills directory (`~/.claude/skills/software-engineering/`). The skill activates automatically on any coding, refactoring, or documentation task. Specific protocols trigger on keywords:

| Say... | Triggers |
|---|---|
| "refactor" | Confirm tests are green before touching structure |
| "make a slice" / "do this in one pass" | Declare scope, exclusions, done-criteria |
| "break this down" / long multi-step task | Decompose into independent slices with TaskCreate |
| "document this" / "log the decision" | Decision template (30-word rule enforced) |
| "how do we test this" / "how do we verify" | AI proposes cases; deterministic tool gives green/red |
| "refresh checkpoint" / end of session | Overwrite SESSION_CHECKPOINT.md |

---

## Core principle

A slice is the unit of work. It has a declaration, a test, a code trail entry, and a commit. Everything else is noise.

---

## Documents produced

Every slice, the skill drives Claude to maintain the following files:

| File | When updated | Contents |
|---|---|---|
| `SESSION_CHECKPOINT.md` | After major slice / before session ends | Current progress, next atomic actions, uncommitted items; cold-start entry point |
| `docs/slice-plan.md` | When tasks are decomposed | Slice names, goals, done-criteria, status (✅/🅿️) |
| `docs/code-trail.md` | Appended on each slice completion | Change log precise to `file:symbol`, with entry point and test coverage |
| `docs/tech-spec.md` | When a traceable decision is made | Fixed template: one-sentence decision + rejected alternatives + when to revisit |
| `docs/architecture.md` | When module boundaries change | Component diagram, data flows, reverse index to decision records |

These five files form a complete traceability chain: **slice plan** says what to do → **code trail** says what was done → **tech spec** says why → **architecture** says what the whole looks like → **SESSION_CHECKPOINT** says where we are now and what's next.

---

## Examples

### Slice plan

![Slice plan example](docs/images/image-20260608103416291.png)

### Decision record

![Decision record example](docs/images/image-20260608103633603.png)

### Code trail

![Code trail example](docs/images/image-20260608103937937.png)
