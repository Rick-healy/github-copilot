---
name: project-delivery
description: Multi-week project delivery workflow — requirements gathering, specifications, approach planning, standards setting, and execution. Use when the user says they want to run a "project delivery", start a new multi-phase project, or references phases like "requirements → spec → plan → build". Produces artifacts under a `0.Delivery/` folder in the workspace. Do NOT use for single-day tasks (use the quick-task skill instead).
---

# Project Delivery

Orchestrate a five-phase workflow for delivering a new project. All artifacts go under `0.Delivery/` in the workspace root.

## When to use

- User is starting a project that will take multiple weeks.
- There are meeting minutes, existing requirements docs, or supporting assets to organise.
- The user explicitly asks for the "project delivery" workflow.

If any of these are false, ask before proceeding — the [quick-task skill](../quick-task/SKILL.md) is usually a better fit.

## Setup

Before Phase 1:

1. Ask the user for the high-level project goal in one sentence.
2. Ask for a short project name (used in folder / file paths).
3. Create a project-specific instructions file so context is loaded on every request in this workspace:

   **File:** `.github/instructions/project-<projectname>.instructions.md`

   ```markdown
   ---
   applyTo: '**'
   description: 'Project-specific context for <projectname>'
   ---
   # <Project name>

   ## Summary
   <one paragraph — filled in during Phase 1>

   ## Key files
   <populated as phases progress>
   ```

4. Confirm each phase's completion with the user before moving on.

---

## Phase 1 — Requirements

**Folder:** `0.Delivery/1.Requirements/`

Ask the user to gather source materials (meeting minutes, emails, existing user stories, BDD specs, acceptance criteria) and drop them into `source-materials/` and `structured-requirements/` respectively. Convert non-Markdown to Markdown where feasible.

Produce **`0.Delivery/1.Requirements/requirements.md`** with:

- Executive Summary
- Objectives (link to `objectives/objectives.md`)
- Functional Requirements
- Non-Functional Requirements (performance, security, usability)
- Constraints
- Assumptions
- References

If clear objectives don't exist in the source materials, produce **`0.Delivery/1.Requirements/objectives/objectives.md`** with primary goals, measurable success criteria, and constraints.

Update the project instructions file's **Key files** section with pointers to what you just created.

**Gate:** requirements are testable, success criteria are measurable, user confirms completeness.

---

## Phase 2 — Specifications & assets

**Folder:** `0.Delivery/2.Specifications/`

Create this structure:

```
2.Specifications/
├── schemas/
├── examples/
│   ├── inputs/
│   ├── outputs/
│   └── configurations/
├── documentation/
├── diagrams/
├── standards/
└── reference/
```

Have the user drop assets into the appropriate subfolder. For each subfolder, create an **`index.md`** listing each file, its purpose, and any processing notes. Binary files get a Markdown summary alongside them.

Produce **`0.Delivery/2.Specifications/analysis.md`** identifying patterns, dependencies between assets, potential challenges, and any missing assets you'd expect to see.

Update the project instructions file's **Key files** section with pointers to key schemas, examples, and specs.

**Gate:** all critical assets collected and indexed, analysis complete.

---

## Phase 3 — Approach

**Folder:** `0.Delivery/3.Implementation/`

Analyse the requirements + assets and present **2–3 implementation approaches**. For each:

- Overview (one paragraph)
- Pros
- Cons
- Effort (High / Medium / Low)
- Skills required
- Key risks

Ask the user to pick one. If they're unsure, recommend one with reasoning.

Produce **`0.Delivery/3.Implementation/implementation-plan.md`**:

```markdown
# Implementation Plan

## Selected approach
<name> — <one paragraph rationale>

## Goals
Each goal is completable in a ~3-hour focused session.

### Goal 1: <name>
**Deliverables:** <list>
**Sub-steps:** <ordered list>

### Goal 2: …

## Success criteria
- [ ] <measurable>

## Risks & mitigation
- <risk> → <mitigation>
```

Also produce **`0.Delivery/3.Implementation/progress-checklist.md`** with a nested checklist mirroring the goals.

Update the project instructions file with the selected approach and milestone list.

**Gate:** goals are 3-hour-sized, success criteria measurable, user approves plan.

---

## Phase 4 — Standards

**Folder:** `0.Delivery/4.Standards/`

Based on the selected approach, add technical standards to the project instructions file. Include the sections relevant to the chosen stack (Java / Python / TypeScript / .NET / etc.) plus universally-applicable sections:

- **Testing standards** — coverage targets, test organisation, mocking strategy, CI integration.
- **Quality assurance** — code review process, documentation requirements, security guidelines, performance standards, logging.
- **Project-specific** — data transformation rules, error handling strategy, configuration management, integration patterns.

Produce **`0.Delivery/4.Standards/standards-checklist.md`** — a checklist covering code quality, testing, security, documentation, performance, and integration. This is used as the acceptance gate in Phase 5.

**Gate:** standards are appropriate for the stack, testing strategy covers unit + integration + performance, checklist is comprehensive.

---

## Phase 5 — Execution

Working folder: workspace root, or a dedicated `project/` subfolder — ask the user.

For each goal in the implementation plan:

1. **Setup (~15 min)** — review objectives, prepare env, agree working folder.
2. **Implementation (~2.5 hr)** — follow sub-steps, apply standards, write tests as you go, document deviations.
3. **Validation (~15 min)** — deliverables vs. success criteria, run tests, update progress checklist, commit with a clear message.

Maintain **`0.Delivery/3.Implementation/session-log.md`** — one entry per work session recording what was done, what was blocked, what's next.

When encountering issues: document → try alternative → escalate to user if still blocked after ~30 min → update the plan if scope has changed.

**Final checkout:** all functional requirements met, all tests passing, standards checklist ticked, docs current, performance benchmarks hit. Produce a `lessons-learned.md`.

---

## Handoffs during a project

Within each phase you can (and should) delegate to the built-in agents:

- **Planning-heavy phases (1, 2, 3, 4)** — pair with the `Planner` agent.
- **Phase 5 execution** — pair with the `Implementer` agent for each goal, then the `Reviewer` agent before checking a goal off.

## Anti-patterns

- Don't run all phases in one turn. Each phase has a user gate for a reason.
- Don't start Phase 5 until Phase 3's plan is approved.
- Don't skip Phase 4 — the standards checklist is what makes Phase 5's gates meaningful.
- Don't duplicate content between the project instructions file and the `0.Delivery/` artifacts — instructions link, artifacts contain.
