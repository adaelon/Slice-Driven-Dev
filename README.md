# Slice-Driven Dev

一个 Claude Code Skill，让 AI 在写代码时遵守工程纪律——少花时间善后，多花时间交付。

[English version below](#english)

---

## 它能帮你做什么

加载这个 skill 后，Claude 会在正确的时机自动触发对应的协议：

**意图不清楚先追问，不猜测。** 任务模糊时，Claude 每次只问一个最关键的问题，问到对意图有 ≥95% 把握才动手。不会先建一堆假设，做完再让你推倒重来。

**用极小的切片工作，每刀可验证。** 动手前先声明：这刀做什么、明确不做什么、怎么算完成。功能和重构不混在一起，"顺便"两个字出现就是切片污染。

**保持测试覆盖。** 改了已有路径就跑测试；加了新分支就写一条覆盖它的测试；修 bug 先写能复现的失败测试再修。没有测试框架时，Claude 会告诉你它手动验证了什么，而不是假装没问题。

**AI 生成输入，确定性工具判断输出。** Claude 出方案、写代码、列边界用例；编译器、测试 runner、linter 给出对错。"我看了一下感觉没问题"不算验证。

**文档是工作的副产品，不是尾声的总结。** 每个切片完成时同步产出三样东西：代码链路条目（文件:符号 → 改了什么）、决策记录（选了什么、否决了什么）、架构更新（模块边界变了才触发）。决策模板有硬约束——一句话 30 字以内，压不出来说明决策本身还没想清楚。

**大任务先拆切片再动手。** 涉及多文件、多步骤的任务，必须先分解成独立可提交的单元。每个单元只依赖文件状态，不依赖"我们刚才聊过的那个值"。

**会话切换前刷新热启动盘。** 大切片完成或会话即将结束时，Claude 会整页覆写 `SESSION_CHECKPOINT.md`：当前进度、下一步的原子动作、未提交的内容。下一个会话读这个文件，零摩擦接手。

---

## 适合谁

用 Claude Code 做有一定规模的工程工作的人——尤其是任务横跨多个会话、多个文件，或者有需要日后可追溯的决策时。

---

## 使用方法

把 `SKILL.md` 放到 Claude Code 的 skills 目录（`~/.claude/skills/software-engineering/`）。只要是写代码、重构、写技术文档的任务，skill 自动激活。说出以下关键词时触发对应协议：

| 说出... | 触发 |
|---|---|
| "重构" | 先确认测试全绿，再动结构 |
| "做个切片" / "做这一刀" | 声明范围、排除项、完成判据 |
| "拆任务" / 长的多步骤任务 | 分解成独立切片，用 TaskCreate 落盘 |
| "落档" / "写进文档" / "落进文档" | 决策模板（30 字规则强制执行） |
| "这个怎么验" / "怎么测" | AI 出用例，确定性工具给绿/红 |
| "刷新 checkpoint" / 会话结束前 | 整页覆写 SESSION_CHECKPOINT.md |

---

## 核心原则

切片是工作的基本单位。它有声明、有测试、有代码链路条目、有 commit。其他的都是噪音。

---

<a name="english"></a>
## English

A Claude Code skill that enforces disciplined software engineering habits when working with AI — so you spend less time untangling messes and more time shipping.

**Clarify before coding.** One targeted question at a time, until intent is clear enough to act (≥95% confidence). No building on guesses.

**Work in tiny, verifiable slices.** Declare scope, exclusions, and done-criteria upfront. No mixing features with refactors, no "while I'm at it" creep.

**Keep tests green.** Changed a path → run tests. New branch → write a test. Bug fix → failing test first, then fix.

**AI generates inputs; deterministic tools judge outputs.** Compiler/test runner/linter decides correctness, not "I checked and it looks fine."

**Document continuously.** Every slice produces: a code trail entry, a decision record, and an architecture update if boundaries changed. Decision template is kept tight — 30-word limit, or the decision isn't clear yet.

**Break large tasks into slices before starting.** Multi-file, multi-step work gets decomposed into independent, committable units first. Each depends only on file state.

**Refresh the session checkpoint before switching context.** Claude overwrites `SESSION_CHECKPOINT.md` with the minimum a fresh session needs: what's in progress, next concrete actions, uncommitted work.

**Core principle:** A slice is the unit of work. Declaration + test + code trail + commit. Everything else is noise.
