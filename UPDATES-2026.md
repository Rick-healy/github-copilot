# Updates 2026 — Migrating this repo to the modern Copilot customization model

> A narrative of how GitHub Copilot's customization surface has evolved since this repo was first created, and what needs to change to stay current. Written to give demo audiences the "why" behind each move, not just the "what".

---

## Where we started

When this repo was put together, GitHub Copilot in VS Code gave you three levers to shape its behaviour:

1. **`.github/copilot-instructions.md`** — a single always-on instruction file for the whole repo.
2. **`.github/chatmodes/*.chatmode.md`** — custom "chat modes" you selected from a picker to swap persona + tool set.
3. **`.github/prompts/*.prompt.md`** — reusable prompt files, invoked by pasting `#file:...` references into chat.

That's what you see today: a single [`SoftwareEngineer.chatmode.md`](.github/chatmodes/SoftwareEngineer.chatmode.md), a lean [`copilot-instructions.md`](.github/copilot-instructions.md), and 12 prompt files chained together into two "orchestrators" ([project delivery](.github/prompts/project-delivery-orchestrator.prompt.md) and [quick task](.github/prompts/quick-task-orchestrator.prompt.md)).

At the time, this was the state of the art. Chat modes were the only way to bind "persona + tools + model" together, and the only way to compose a multi-step workflow was to have Copilot manually walk through a chain of prompt files using `#file:` references.

## What changed

Between then and now (mid-2026), VS Code has quietly retired chat modes as a top-level concept and re-organised customization into a richer, more composable model. From the current [Agent customization overview](https://code.visualstudio.com/docs/agents/concepts/customization):

| Goal | The 2026 primitive |
|---|---|
| Apply the same coding standards to all code | **Always-on instructions** (`copilot-instructions.md` or `AGENTS.md`) |
| Apply different rules to different file types | **File-based instructions** (`.instructions.md` with `applyTo`) |
| Give the AI a focused role with limited tools | **Custom agents** (`.agent.md`) |
| Automate a repeatable multi-step workflow (with scripts) | **Agent skills** (`SKILL.md`) |
| Run a repeatable task on demand | **Prompt files** (`.prompt.md`) |
| Connect to external systems | **MCP servers** (`.vscode/mcp.json`) |
| Run something deterministically at a lifecycle point | **Hooks** |

Two things worth flagging:

- **Chat modes are gone from the customization docs.** The `.chatmode.md` format still loads for now, but VS Code no longer lists it as a customization option. Everything a chat mode did — persona, tools, model preference — is done better by a **custom agent**, which also brings subagents, handoffs, per-agent hooks, model fallback lists, and an official `/agents` picker.
- **Prompt files got frontmatter.** They're no longer "just markdown you paste into chat" — they now support YAML frontmatter for `agent`, `model`, `tools`, `description`, and `argument-hint`, and they surface as real `/slash-commands` in the chat input.

## What this repo needs to change

### 1. Chat mode → Custom agent

`SoftwareEngineer.chatmode.md` should become one or more `.agent.md` files under `.github/agents/`. This unlocks the thing the chat mode couldn't do: **handoffs**.

Instead of one giant "Software Engineer" mode that tries to do requirements, planning, and implementation, we can now express the workflow as a small graph of specialised agents:

- `planner.agent.md` — read-only tools, produces a plan
- `implementer.agent.md` — edit + terminal tools, executes the plan
- `reviewer.agent.md` — read-only tools, checks against standards

...with `handoffs:` in the frontmatter wiring them together. The user gets a "Start Implementation" button at the end of the planning turn — no manual copy-paste of `#file:` references.

Other issues in the current chat mode file that need fixing regardless:

- `model: claude-4` isn't a valid model id anymore. Current values look like `Claude Sonnet 4.5`, `Claude Opus 4.5`, `GPT-5.2`, or the qualified form `Claude Sonnet 4.5 (copilot)`. The new format also supports an **array** as a fallback list: `model: ['Claude Opus 4.5', 'GPT-5.2']`.
- The `tools:` list uses old bare names. Tools are now namespaced (`search/codebase`, `search/usages`, `web/fetch`, `edit`, `read/terminalLastCommand`, `vscode/askQuestions`). Several entries in the current list (`new`, `openSimpleBrowser`, `runNotebooks`, `searchResults`, `vscodeAPI`) are either renamed or gone.

### 2. Orchestrator prompts → Agent Skills

The 5-phase project-delivery workflow and the 4-phase quick-task workflow are the textbook definition of an **Agent Skill**: a multi-step deterministic procedure that the model reads once and then executes, potentially with bundled scripts and templates.

Today they're implemented as a chain of prompt files that reference each other with `#file:` — which works, but has three problems:

1. The model has to *find* and *follow* the chain manually.
2. There's no way to bundle helper scripts (e.g. "create the `0.Delivery/` folder structure") alongside the instructions.
3. There's no discoverability — you can't `/find` a skill from chat the way you can invoke a prompt.

Migrating these to `.github/skills/project-delivery/SKILL.md` and `.github/skills/quick-task/SKILL.md` keeps all the phase logic but makes them first-class, discoverable, and packageable.

### 3. Prompt files need frontmatter

Every `.prompt.md` in this repo is missing its YAML header. That's not a hard error — they still run — but it means:

- No `description` in the `/` picker
- No pinned `model` (so the user's currently-selected model is used, which may not be the right one for planning vs coding)
- No `tools:` scoping (so the prompt gets whatever the current agent has)
- No `argument-hint` guiding the user on how to invoke

Minimum viable frontmatter for each:

```yaml
---
description: 'One line shown in the / picker'
agent: agent      # or 'ask' / 'plan' / a custom agent name
model: Claude Sonnet 4.5
tools: ['search/codebase', 'edit', 'vscode/askQuestions']
argument-hint: 'e.g. "refactor the auth module"'
---
```

### 4. `#file:` references → slash commands and Markdown links

Throughout the current prompts and chat mode you'll see things like:

```
#file:../prompts/project-delivery-orchestrator.prompt.md "Your project goal here"
```

That syntax is legacy. In the current UX:

- Prompt files are invoked as `/prompt-name` slash commands once they live in `.github/prompts/`.
- Cross-references between customization files are just standard Markdown links.
- User input mid-prompt is collected with `${input:variableName}` or the `vscode/askQuestions` tool — not by asking the user to paste more `#file:` refs.

### 5. Consider adding `AGENTS.md`

Alongside `copilot-instructions.md`, the industry has converged on a cross-tool standard: **`AGENTS.md`** at the repo root, read by GitHub Copilot, OpenAI Codex, Anthropic Claude Code, Cursor, and others. If you want the same standards to apply regardless of which AI tool a developer picks up, this is the file to write them in. `copilot-instructions.md` can then be reduced to Copilot-specific additions (or removed).

### 6. Missing pieces worth adding

- **`.vscode/mcp.json`** — repo-scoped MCP server config so anyone opening the workspace gets the same tools connected (databases, docs servers, internal APIs).
- **Hooks** — e.g. auto-run a formatter after every edit, or block a commit that doesn't have tests.
- **Broader instruction coverage** — currently only Java, Python, and XML/XSD. Common gaps: TypeScript/JavaScript, C#, Go, Rust, Bicep/Terraform, SQL, Dockerfile, Markdown, YAML.
- **LICENSE** — for a template repo meant to be copied into other repos, a permissive licence (MIT/CC0) is expected.

## The story to tell in the demo

If you're walking someone through this repo live, the arc is:

> "Originally you had one file, `copilot-instructions.md`, and everything else was manual prompting. Then chat modes let us bind a persona to a tool set. Then prompt files let us package reusable prompts. That got us to what you see in this repo today."
>
> "In 2026 the model matured. VS Code split those responsibilities apart: **instructions** for the *how*, **custom agents** for the *who*, **agent skills** for the *what* (multi-step workflows), **prompt files** for one-shot tasks, **MCP** for external reach, and **hooks** for deterministic actions. Chat modes went away — they're now just a special case of custom agents. And workflows that used to be chains of prompt files are now first-class skills."
>
> "So what we're upgrading in this repo is: the chat mode becomes a set of custom agents with handoffs, the orchestrator prompts become skills, every prompt file gets proper frontmatter, and we add the pieces that didn't exist before — MCP config, hooks, and an `AGENTS.md`."

## Migration checklist

- [ ] Create `.github/agents/` and split `SoftwareEngineer.chatmode.md` into `planner.agent.md`, `implementer.agent.md`, `reviewer.agent.md` with handoffs
- [ ] Update model IDs and tool names to current values
- [ ] Create `.github/skills/project-delivery/SKILL.md` from the 5-phase orchestrator
- [ ] Create `.github/skills/quick-task/SKILL.md` from the 4-phase orchestrator
- [ ] Add YAML frontmatter (`description`, `agent`, `model`, `tools`) to every `.prompt.md`
- [ ] Replace `#file:` invocations with slash commands and Markdown links
- [ ] Add `AGENTS.md` at repo root (portable across Copilot / Codex / Claude / Cursor)
- [ ] Add `.vscode/mcp.json` starter
- [ ] Add hooks under `.github/hooks/` (e.g. post-edit formatter)
- [ ] Broaden `.github/instructions/` coverage (TS/JS, C#, Bicep, SQL, Dockerfile, Markdown, YAML at minimum)
- [ ] Add a LICENSE
- [ ] Rewrite `README.md` around the 2026 model

## References

- [Agent customization overview](https://code.visualstudio.com/docs/agent-customization/overview)
- [Customization decision matrix](https://code.visualstudio.com/docs/agents/concepts/customization)
- [Custom agents](https://code.visualstudio.com/docs/agent-customization/custom-agents)
- [Agent skills](https://code.visualstudio.com/docs/agent-customization/agent-skills)
- [Prompt files](https://code.visualstudio.com/docs/agent-customization/prompt-files)
- [Custom instructions](https://code.visualstudio.com/docs/agent-customization/custom-instructions)
- [Hooks](https://code.visualstudio.com/docs/agent-customization/hooks)
- [MCP servers](https://code.visualstudio.com/docs/agent-customization/mcp-servers)
