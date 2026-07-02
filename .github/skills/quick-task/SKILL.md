---
name: quick-task
description: Streamlined 4-phase workflow for single-day tasks (max ~8 hours) — objective, approaches, plan, execute. Use for refactors, bug fixes, small features, or focused analyses. Produces artifacts under `0.Delivery/<task-name>/`. Do NOT use for multi-week projects (use the project-delivery skill instead).
---

# Quick Task

Orchestrate a four-phase workflow for a single-day task. Everything the task produces goes under `0.Delivery/<task-name>/` in the workspace root.

## When to use

- Task fits inside one working day (≤ ~8 hours).
- Focused, single-objective: a refactor, a bug fix, a small feature, an analysis.
- Not a full project delivery — no requirements gathering, no schemas, no multi-stakeholder review.

If the request needs meeting minutes / schema analysis / weeks of work → suggest the [project-delivery skill](../project-delivery/SKILL.md) instead.

## Setup

Ask the user to describe the task in one or two sentences. Derive a short `<task-name>` (kebab-case) from it and create `0.Delivery/<task-name>/`.

---

## Phase 1 — Objective

Ask up to 3 questions maximum to clarify:

1. **What** specifically needs to happen?
2. **Why** — one-sentence context.
3. **Which files / components** are involved?

Define 2–4 measurable **success criteria** with the user (e.g. "function X handles empty input without erroring", "existing tests still pass", "P95 latency < Y ms").

Check for constraints: deadline, out-of-scope items, required tools/patterns, backward-compatibility.

Produce **`0.Delivery/<task-name>/objective.md`**:

```markdown
# Task: <title>

## Goal
<1–2 sentences>

## Success criteria
- [ ] <measurable outcome>

## Scope
**In:** <list>
**Out:** <list>

## Constraints
- <constraint>

## Context
**Files:** `path/one`, `path/two`
**Related:** <components / modules>

## Background
<1–2 sentences of why>
```

**Gate:** objective is clear, success criteria measurable, user confirms.

---

## Phase 2 — Approaches

Present **2–3 approaches**. Typical shapes:

- **Quick & simple** — minimal changes, low risk, may be less elegant.
- **Optimal & clean** — better design, more time, more maintainable.
- **Hybrid** — balance.

For each: overview, key steps, pros, cons, time estimate, risk (Low/Medium/High), files to modify.

Produce **`0.Delivery/<task-name>/approach-options.md`** with the options and a **Selected approach** section recording the user's choice and rationale.

**Gate:** 2–3 approaches presented, user picks one, choice is documented.

---

## Phase 3 — Plan

Break the selected approach into **3–7 concrete steps**. Each step:

- Specific (name the file, name the function).
- Testable (validation criterion).
- Ordered (logical sequence).
- Sized (30 min – 2 hours).

Produce **`0.Delivery/<task-name>/plan.md`**:

```markdown
# Plan: <task name>

## Overview
**Approach:** <selected>
**Estimated total:** <hours>
**Risk:** Low/Medium/High

## Pre-execution
- [ ] Files identified and accessible
- [ ] Dev env ready
- [ ] Tests can be run
- [ ] Backup/branch created if needed

## Steps

### 1. <Action-oriented title>
**Objective:** <what this achieves>
**Actions:**
- [ ] <specific action>
**Files:** read `x`, modify `y`
**Validation:** <how to verify>
**Estimate:** <time>

### 2. …

## Risks
- <risk> → <mitigation>

## Rollback
1. <step>

## Final validation
- [ ] All steps executed
- [ ] All success criteria from objective.md met
- [ ] Tests passing
```

Also produce **`0.Delivery/<task-name>/progress.md`** — one entry per step with `⏳ Not Started` / `⏳ In Progress` / `✅ Complete`, start time, actual time, notes; plus an Issues Log and a Time Log table.

**Gate:** 3–7 steps, each with deliverables and validation, user approves.

---

## Phase 4 — Execute

For each step:

1. Mark it `⏳ In Progress` in both `plan.md` and `progress.md`, note start time.
2. Read the files, make the change, run tests incrementally.
3. When done: run the validation from the plan, mark `✅ Complete`, update `progress.md` with what was done, actual time, and any issues.
4. Update the overall progress percentage.
5. Move to the next step.

**Handling issues:**
- Minor → fix and continue.
- Medium → inform the user, propose fix, continue.
- Major blocker → stop, discuss options, update the plan if scope has genuinely changed.

Log every non-trivial issue in `progress.md`'s Issues Log with: step number, description, impact, resolution, time impact.

**Final validation** when all steps are done:
- Verify each success criterion from `objective.md`.
- Run the full relevant test suite.
- Code quality check: readable, error handling in place, no debug code left, docs updated where changed behavior warrants it.

Update `progress.md` with a **Summary** section: completion time, total time, steps completed, success criteria met, files created/modified/deleted, tests, recommendations for follow-ups.

**Gate:** all success criteria met, tests passing, user confirms.

---

## Handoffs during a quick task

- **Phases 1–3** — pair with the `Planner` agent.
- **Phase 4** — pair with the `Implementer` agent, and optionally the `Reviewer` before marking a step complete.

## Anti-patterns

- Don't skip Phase 1 — a fuzzy objective produces a fuzzy result.
- Don't skip Phase 2 — the first approach that comes to mind is often not the best.
- Don't stack more than 3 phases in a single turn — the user gates matter.
- Don't let a "quick task" grow to multiple days. If it does, stop and escalate to a project delivery instead.
