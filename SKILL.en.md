# Software Engineering Discipline

> **[中文版 →](SKILL.md)**

> This skill is not a one-time checklist — it is a set of **sub-protocols that trigger at different moments**. Each has its own trigger condition, required actions, and prohibitions.
> **How to use**: At the start of a task, scan the "trigger keyword table" at the bottom, identify which protocols apply, and follow them.

---

## Auto-activation

This skill activates by default whenever the task involves:
- Writing / changing / refactoring / debugging / adding features / adding tests
- Writing technical docs / design specs / decision records / architecture notes
- Discussing "how to approach this" when the discussion will be committed to writing

No explicit invocation needed. But when the user says a **trigger phrase** (see table at the bottom), the corresponding protocol is **mandatory** — no skipping.

---

## Pre-flight: Intent Clarification

Core belief: **without aligning on true intent, every slice downstream is a guess.**

### §0 Confidence-Triggered Clarification Protocol

**Trigger**: Before taking any SWE task (write code / change code / design / write docs / log a decision), self-assess — "How confident am I in the user's **true intent**?"

- Confidence **≥95%** → Act immediately, don't interrupt the user.
- Confidence **<95%** → Enter the clarification loop until ≥95% before proposing anything.

**Protocol**:
1. Pick the **one ambiguity that most affects direction**. Test: would different answers lead to fundamentally different solutions? If not, it's not worth asking.
2. **One question at a time.** Never fire 3–4 at once — each follow-up should depend on the previous answer.
3. Use **AskUserQuestion** (with options when there are clear candidates) or plain text (for open questions).
4. After each answer → **re-assess confidence** → if still <95%, ask the next question; if ≥95%, move to the proposal.
5. Before proposing, **briefly restate your understanding** ("Both aligned: X / Y") — give the user one last chance to correct.

**Prohibitions**:
- No writing code or producing a long proposal before reaching 95%.
- No disguising confirmation requests as clarification — "Should I do X?" is a permission ask, not clarification. **Clarifying questions must genuinely change direction.**
- No asking things you can figure out yourself; no asking about stylistic preferences (colors, naming) while ignoring strategic ambiguity.
- No fishing for questions when the user has already been clear.

**Common traps that inflate confidence** (any of these usually means confidence is actually <95%):
- The task has multiple reasonable interpretations and you silently picked one;
- The user didn't specify a **key parameter** (scope, priority, boundary);
- The task resembles a familiar pattern but isn't quite the same;
- There's a "should we also do X" tradeoff lurking;
- The requirement contains two or more non-independent ambiguities.

**Overrides**:
- User says "just do it / don't ask / use your judgment" → skip §0, proceed with best guess, **explicitly label assumptions** in the output.
- User's request is already very detailed → you may hit ≥95% immediately, act.
- After **4 rounds** still below 95% → stop asking, **list your current interpretations** and let the user choose.

---

## Group A: Small-Slice Method

Core belief: **one cut at a time, every cut verifiable.** The failure mode of mixing cuts is "no idea which cut broke things."

### A1. Slice Declaration Protocol

**Trigger**: Before writing or changing any code.

**Protocol**: Answer these 3 points in 1–3 sentences before starting:
1. **What this slice does** (one sentence, verb first)
2. **What it does not do** (explicitly exclude adjacent work that could sneak in)
3. **Done criteria** (how you'll know this cut is complete — usually "X tests pass" or "Y command produces Z")

**Prohibitions**:
- No doing feature + refactor in the same slice; no fixing two unrelated bugs; no "cleaning up" unrelated code.
- No "while I'm at it." That phrase signals slice contamination — pull it into the next slice.

### A2. Test Coverage Contract

**Trigger**: After writing or changing any executable code.

**Protocol**:
- **Changed an existing path** → run related tests, must still be green.
- **Added a new path** (new branch, function, or error handler) → write one minimal test covering it.
- **Fixed a bug** → write a failing test that reproduces it first, then fix (red-green).
- **No test framework** → before declaring the slice done, **tell the user** "no tests here; I manually verified Y with command X." Don't pretend coverage exists.

**Prohibitions**:
- No declaring a slice done without running tests.
- No "adjusting the test" when it goes red — unless you've confirmed the test itself was wrong, with a written reason.

### A3. Refactoring Discipline

**Trigger**: User says "refactor," or you recognize work that changes structure without changing behavior.

**Strict definition of refactoring**:
> **Behavior unchanged + tests unchanged (all green) + internal structure changed + easier to understand afterward.**
> If any condition isn't met, it's not a refactor — use a different protocol.

**Protocol**:
1. Confirm tests exist and are green before touching structure; if no tests, write a characterization test first.
2. **Small steps**: keep tests green at every step. No "change ten things, tests all red, fix at the end."
3. Write a one-sentence refactoring goal: "I want to turn X into Y so that doing Z is easier." If you can't write this sentence, don't start.
4. Done criteria: **a new reader can understand the code without looking at history.**

**Prohibitions**:
- No disguising "add a feature" as a refactor.
- No disguising "fix a bug" as a refactor.
- No mixing refactor + feature in the same commit/slice.

### A4. Long-Task Decomposition (preventing context fragmentation)

**Trigger**: The task is estimated to exceed one session — rough signal: 3+ files with large changes / multi-step serial work / long processes / multi-paragraph task description.

**Protocol**:
1. Before starting, decompose the task into **N independent, verifiable steps**, each with:
   - Clear input/output
   - Done criteria (can run / tests pass / doc is readable)
   - **Depends only on file state, not session memory** (i.e., the next session can take over from scratch)
2. Use TaskCreate to make these steps explicit tasks; **check each off as it completes.**
3. At the end of each step, **commit the result immediately** (code commit-ready, doc written, decision logged). No "record everything at the end."
4. Any urge to "run it all first then handle it" → decompose further immediately.

**Prohibitions**:
- No stuffing a long task into one continuous tool-call stream.
- No relying on "that value I just computed earlier in the conversation" as the sole source for the next step — write it to disk or into a task.

---

## Group B: Precise Intermediate Language for AI Collaboration

Core belief: **pure prose is vague, pure code is rigid, the middle ground is most efficient.**

### B1. Precise Intermediate Language

**Trigger**: Discussing "how to approach this" when the discussion will turn into action (design, interfaces, data flow, algorithms).

**Protocol**: Proposals should use **forms between natural language and code**, not pure prose. Priority order:

1. **Type/function signatures**: `def schedule_next(history: list[Turn], budget: int) -> AgentId | "STOP"`
2. **Pseudocode** (key control flow written out, details in natural language)
3. **State machines / flow diagrams** (text form): `IDLE → PLAN → DEBATE ⇄ HUMAN_INTERJECT → SYNTHESIZE → IDLE`
4. **Data examples** (one concrete input → expected output): use real samples, not "suppose there's an X"
5. **Spec tables** (condition × result): for exhaustive branching

**Prohibitions**:
- No pure-prose proposals like "I think we could build a scheduler that intelligently decides the next speaker based on context."
- No claiming "the design is clear" without one of the above forms.
- No jumping straight to full code during the design phase — stay at pseudocode/signatures until it's actually time to code.

### B2. AI Generates Inputs; Deterministic Tools Produce Outputs

**Trigger**: Discussing "how to verify correctness," designing tests, designing feedback loops, designing agent reward signals.

**Protocol**:
- **What AI is good at**: generating ideas, test inputs, edge cases, suggestions, code, summaries.
- **What AI is bad at**: **judging correctness.** That belongs to deterministic tools — compiler, type checker, test runner, linter, real API responses, spec comparisons.

**In practice**:
- Test design: **AI generates input cases**, **test runner gives green/red** — not "let AI decide if this output looks reasonable."
- Agent reward signals: execution results (code runs / tests pass) as the objective anchor; **LLM-as-judge only as a fallback when no objective anchor exists.**
- User acceptance: **run with real data, observe real output** — not "let AI self-rate."
- Doc review: **diff tools / linter / link checker** — not "let AI read through and say it's fine."

**Prohibitions**:
- No using LLM-as-judge as the sole verification method, unless the task genuinely has no objective criteria.
- No saying "I checked and it's fine" without running a deterministic tool.
- No "AI output + AI scoring" closed loop as reliable feedback — it self-reinforces.

---

## Group C: Continuous Documentation

Core belief: **documentation is a by-product of work, not a summary written at the end.** Every slice should drop three types of residue.

### C1. Decision Logging (write-decision protocol)

**Trigger**: User says "log this / document this / write down the decision." Or you recognize that a conversation produced a decision that will be referenced later.

**Template** (hard shape, do not exceed):

```markdown
### §X.Y Title (noun phrase, e.g., "Scheduler priority mode")

**Decision**: one sentence, ≤30 words.

**Rejected**:
- A: one-line reason.
- B: one-line reason.
- (max 3)

**Constraint**: one line.        <!-- optional — non-obvious constraint -->
**Revisit when**: one line.      <!-- optional — trigger to reconsider -->
**Deep dive**: [link to original discussion or longer argument]
```

**Prohibitions**:
- **No more than 2 heading levels** (main heading + bold sub-labels inside the block).
- **No filler phrases** like "this translates to / in other words / our approach is / breaking this down."
- **No treating the conversation as the document** — the reader is someone months from now, not the person who just talked to you.
- **No decision block longer than 15 lines** (excluding the deep-dive link).
- Can't compress to 30 words → the decision isn't clear yet. **Stop logging, go clarify.**

### C2. Code Trail

**Trigger**: Every time a slice (as defined in A1) completes.

**Protocol**: Append one entry to `docs/code-trail.md` (or equivalent):

```markdown
## YYYY-MM-DD Slice name

**Touched**:
- `path/to/file.py:Class.method` — what changed (one line)
- `path/to/other.py:func` — what was added (one line)

**Entry point**: how this path gets called (user action / API endpoint / test).
**Test**: `tests/test_x.py::test_y` covers what.
```

**Prohibitions**:
- No vague entries like "optimized various parts." Every entry must be precise to file:symbol.
- No waiting to "consolidate at project end" — write it when the slice finishes.

### C3. Architecture

**Trigger**: **Only when structure changes.** Incremental code changes don't trigger this; adding a module, changing module boundaries, changing major data flows, or introducing a new component type do.

**Protocol**: Maintain `docs/architecture.md` (project-level), containing:
1. **Component diagram** (text or mermaid): what modules exist, who calls whom
2. **Data flows**: end-to-end flow for major operations (using B1 state machine/pseudocode form)
3. **Reverse index of key decisions**: each component name links to "why this design" → points to C1 records

**Prohibitions**:
- No letting the architecture doc become a collection of code comments. Its job is to give a blueprint to someone who hasn't read the code.
- No writing from memory — read current state first, then update, so the doc matches real code.
- No letting the architecture doc fall more than one slice period behind.

### C4. SESSION_CHECKPOINT Maintenance

**Trigger**: Any of the following:
- A major slice completes and is committed, and you're about to end the session or switch context;
- User says "refresh checkpoint / update hot-start / wrap up";
- Context window is nearly full (2–3 turns from truncation);
- A long task is being set aside and the next session will need to continue from the middle.

**Protocol**: **Overwrite** (not append) `SESSION_CHECKPOINT.md` with this fixed structure:

```markdown
# SESSION_CHECKPOINT — YYYY-MM-DD HH:MM

## Freshness check
- Commit at write time: <hash> <title>
- On read, compare with `git log -3`; if different, trust git log.

## What's in progress
One sentence: which major slice / feature is active and what the goal is.

## Next steps (ready to hand off)
1. Step one: specific action (file/function/command)
2. Step two: …
(max 5 steps; each must be an immediately executable atomic action)

## Uncommitted / unfinished
- File or module: status (e.g., "changed, untested", "tests red", "pending commit")
- If none: none

## Cold-start reading sequence
Read these files in order to restore full context:
1. `docs/tech-spec.md` — architecture backbone
2. `docs/code-trail.md` — change log
3. (other required files)

## Decisions made this session
- §X.Y Decision name: one-line conclusion (logged to docs/tech-spec §X.Y)
- (omit this section if no decisions)
```

**Prohibitions**:
- **No appending** — every write is a full overwrite; the file always reflects only the latest state.
- **No diary entries** — not "what I did today," but "the minimum an unfamiliar session needs to know."
- **No more than 80 lines** — exceeding this means redundancy; compress.
- "Next steps" must not say "continue feature X" — must be immediately executable atomic actions (file path, function name, command).
- Don't re-expand decisions already logged elsewhere — one-line summary + pointer is enough.

### C5. Session Hot-Start Protocol (reading checkpoint + cold-start sequence)

**Trigger**: At the start of a session, user says "read cold-start / take over / hot-start / read checkpoint" — or you open SESSION_CHECKPOINT.md as the session entry point.

**Protocol** (strict order, no skipping):
1. Read `SESSION_CHECKPOINT.md` — get freshness, current state, cold-start reading sequence.
2. **Check freshness**: does `git log --oneline -1` match the hash at the top of the checkpoint? If not, trust git, and note the discrepancy before proceeding.
3. **Execute the cold-start reading sequence in full**: read every file/section listed under `## Cold-start reading sequence` — all of them, in order.
4. After reading everything, report to the user: current state summary + open issues + immediately actionable next step.

**Prohibitions**:
- **Do not report after reading only the checkpoint** — the checkpoint is a router, not the content. The reading sequence it lists is the actual context.
- No selectively reading only "what seems important" — read every item in the sequence, in order.
- No skipping items because there are many files — if a file is long, read the key sections (the checkpoint usually notes which), but don't skip the item entirely.

---

## Trigger Keyword Table

| User says... or task is... | Must activate | Do not skip |
|---|---|---|
| Any SWE task with ambiguous intent | §0 Intent clarification | One question at a time, ≥95% confidence before acting |
| "document this" / "log the decision" | C1 Decision logging | Hard template shape |
| "refactor" | A3 Refactoring discipline | Confirm tests green first |
| "make a slice" / "do this in one pass" | A1 Slice declaration | Declare input/output/criteria |
| "break this down" / "step by step" / long task | A4 Task decomposition | Write to disk + TaskCreate |
| "how do we verify" / "how do we test" | A2 Tests + B2 Tool verification | No LLM self-assessment |
| "how should we approach this" / "design this" | B1 Intermediate language | Type signatures / pseudocode |
| Any code edit | A1 + A2 | Declare slice first, run tests when done |
| New module added / boundary changed | C3 Architecture update | Complete within same slice period |
| Slice complete | C2 Code trail | Append entry immediately |
| Major slice done / session ending / "refresh checkpoint" / context nearly full | C4 SESSION_CHECKPOINT | Full overwrite, no appending |
| "read cold-start" / "take over" / "hot-start" / reading checkpoint at session start | C5 Hot-start | After reading checkpoint, **must continue** executing every item in its cold-start sequence |

---

## Relationship to other skills

- This skill does not replace `verify`, `code-review`, `run`, and similar action skills — those are **tools**, this skill is **discipline**. They work together: this skill requires "deterministic verification on slice completion"; `verify` is the execution mechanism.
- Works with feedback memory: memory records "watch out for X in this project specifically"; this skill provides the general framework. Memory is individual, this skill is universal.

---

## Self-check (run through before completing any major task)

- [ ] Did I have ≥95% confidence in true intent before acting? Was it genuine, or did I skip §0 with inflated confidence? (§0)
- [ ] How many slices was this work divided into? Does each have declared output and done-criteria? (A1 / A4)
- [ ] Did I run tests on all changed paths? Did new paths get coverage? (A2)
- [ ] If I refactored, was it a true refactor (behavior unchanged + tests unchanged)? (A3)
- [ ] Did design discussions use precise intermediate language, or vague prose? (B1)
- [ ] Was verification done with deterministic tools, or AI self-assessment? (B2)
- [ ] Were decision points logged? Using the C1 template, not long-form prose? (C1)
- [ ] Was the code trail appended? Architecture updated if structure changed? (C2 / C3)
- [ ] At session start, after reading the checkpoint, did I execute every item in its cold-start sequence? (C5)
- [ ] When a major slice completed or the session was ending, was SESSION_CHECKPOINT.md fully overwritten? (C4)
  - Are all "next steps" immediately executable atomic actions (file path / function name / command)?
  - Are all uncommitted / unfinished items listed clearly?

Any "no" without a good reason means the task is not complete.
