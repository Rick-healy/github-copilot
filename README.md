# GitHub Copilot Resources

A collection of reusable GitHub Copilot customization files — chat modes, instruction files, and prompt workflows — designed to be dropped into any repository's `.github/` folder.

## Repository Structure

```
.github/
├── copilot-instructions.md              # Global Copilot behaviour (style, tone)
├── chatmodes/
│   └── SoftwareEngineer.chatmode.md     # Expert software engineer chat mode
├── instructions/
│   ├── java.instructions.md             # Java coding conventions
│   ├── python.instructions.md           # Python coding conventions
│   └── xml-xsd.instructions.md          # XML/XSD best practices
└── prompts/
    ├── project-delivery-orchestrator.prompt.md   # Multi-phase project delivery
    ├── quick-task-orchestrator.prompt.md          # Single-day task workflow
    ├── project-delivery/                          # Sub-prompts for project delivery
    │   ├── 1.project-requirements-gatherer.prompt.md
    │   ├── 2.project-specifications-collector.prompt.md
    │   ├── 3.project-approach-planner.prompt.md
    │   ├── 4.project-standards-setter.prompt.md
    │   └── 5.project-implementation-executor.prompt.md
    └── quick-task/                                # Sub-prompts for quick tasks
        ├── 1.task-objective.prompt.md
        ├── 2.task-approaches.prompt.md
        ├── 3.task-plan.prompt.md
        └── 4.task-execute.prompt.md
```

## What's Included

### Global Instructions — `copilot-instructions.md`

Applies to every Copilot interaction in the repo. Currently sets style and tone rules (plain, concise language; validate work before considering it complete).

### Chat Mode — `SoftwareEngineer.chatmode.md`

A custom chat mode that configures Copilot as an expert software engineer. It:

- Uses the Claude 4 model with a broad set of tools enabled.
- Suggests the prompt workflows below at the start of a conversation.
- Enforces testing standards (80%+ coverage, parameterized tests, mirrored test structure).

**To use:** Select **Software Engineer** from the chat mode picker in VS Code.

### Language Instructions

Auto-applied to matching files via `applyTo` globs — no manual invocation needed.

| File | Applies To | What It Covers |
|------|-----------|----------------|
| `java.instructions.md` | `**/*.java` | Oracle/Google style, records, pattern matching, `var`, null handling with `Optional`, SonarQube rule table, Maven/Gradle build checks |
| `python.instructions.md` | `**/*.py` | PEP 8/257, type hints, f-strings, dataclasses, pathlib, custom exceptions, static analysis tooling prompts |
| `xml-xsd.instructions.md` | `**/*.xml`, `**/*.xsd`, `**/*.xsl`, `**/*.xslt` | Namespace conventions, attributes vs elements, schema-first design, versioning, XSD type definitions |

### Prompt Workflows

Two orchestrated prompt workflows that guide Copilot through structured, multi-step processes.

#### 1. Project Delivery (multi-week)

A 5-phase process covering requirements → specifications → approach → standards → implementation. Artifacts are written to a `0.Delivery/` folder.

```
/project-delivery-orchestrator "Your project goal here"
```

Phases (each has its own sub-prompt in `prompts/project-delivery/`):

1. **Requirements Gathering** — collects and organises all project requirements.
2. **Specifications Collector** — gathers supporting docs, examples, and assets.
3. **Approach Planner** — presents implementation options and creates a plan.
4. **Standards Setter** — defines coding and testing standards for the project.
5. **Implementation Executor** — executes development with progress tracking.

#### 2. Quick Task (single day, max 8 hours)

A streamlined 4-phase process for focused tasks like refactoring, bug fixes, or small features.

```
/quick-task-orchestrator "Your task description here"
```

Phases (each has its own sub-prompt in `prompts/quick-task/`):

1. **Objective** — clarifies what needs doing and defines success criteria.
2. **Approaches** — presents 2-3 options with pros/cons and time estimates.
3. **Plan** — breaks work into 3-7 concrete steps.
4. **Execute** — implements the plan with real-time progress tracking.

## How to Use

1. **Copy the `.github/` folder** into the root of your target repository.
2. **Open the repo in VS Code** with GitHub Copilot installed.
3. Language instructions apply automatically when you edit matching files.
4. Switch to the **Software Engineer** chat mode for the full experience.
5. Invoke a prompt workflow by typing the slash command or referencing the file:
   ```
   /project-delivery-orchestrator "description"
   /quick-task-orchestrator "description"
   ```

## Contributing

Feel free to add new instruction files, chat modes, or prompt workflows and submit a PR.