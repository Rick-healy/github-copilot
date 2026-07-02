# GitHub Copilot Customization — Template Repo

A drop-in template of GitHub Copilot customization for VS Code, using the current (2026) primitives: **agents**, **skills**, **instructions**, **MCP**, and a cross-tool **`AGENTS.md`**.

> This repo was updated in July 2026. If you're wondering what changed and why, read [`UPDATES-2026.md`](UPDATES-2026.md) — it's a narrative of the migration from the old chat-mode + prompt-chain model. Anything superseded is preserved under [`0.archive/`](0.archive/).

## What you get

```
├── AGENTS.md                          # Cross-tool repo conventions (Copilot / Codex / Claude / Cursor)
├── LICENSE                            # MIT
├── UPDATES-2026.md                    # Migration narrative
├── .vscode/
│   └── mcp.json                       # Repo-scoped MCP servers
├── .github/
│   ├── copilot-instructions.md        # Copilot-specific style/tone (always-on)
│   ├── agents/                        # Custom agents (personas + tools + handoffs)
│   │   ├── software-engineer.agent.md #   General default; delegates to the others
│   │   ├── planner.agent.md           #   Read-only, produces plans → hands off to Implementer
│   │   ├── implementer.agent.md       #   Executes plans → hands off to Reviewer
│   │   └── reviewer.agent.md          #   Read-only, reviews changes
│   ├── skills/                        # Multi-step workflows
│   │   ├── project-delivery/SKILL.md  #   5-phase multi-week project workflow
│   │   └── quick-task/SKILL.md        #   4-phase single-day task workflow
│   └── instructions/                  # Language / file-type rules (auto-applied)
│       ├── typescript.instructions.md
│       ├── csharp.instructions.md
│       ├── python.instructions.md
│       ├── java.instructions.md
│       ├── bicep.instructions.md
│       ├── xml-xsd.instructions.md
│       └── markdown.instructions.md
└── 0.archive/                         # Legacy chatmodes + prompt files (safe to delete)
```

## How the pieces fit together

The current customization model separates concerns cleanly. Each piece has one job:

| Primitive | Answers the question | Kicks in |
|---|---|---|
| **`AGENTS.md`** + **`copilot-instructions.md`** | "How should the AI behave in this repo?" | Every request |
| **Instructions** (`.instructions.md`) | "How is *this kind of file* written here?" | Automatically, when files match `applyTo` |
| **Custom agents** (`.agent.md`) | "Who is the AI right now?" (persona + tools + model) | You pick from the `/agents` menu |
| **Skills** (`SKILL.md`) | "Run this multi-step workflow" | The AI invokes when the task matches |
| **MCP** (`.vscode/mcp.json`) | "What external systems can the AI reach?" | Automatically, when the config loads |

## Quick start

1. **Copy this repo's contents into your project** (`AGENTS.md`, `LICENSE`, `.vscode/mcp.json`, and the whole `.github/` folder). Skip `0.archive/` and `UPDATES-2026.md` — they're historical.
2. **Open your project in VS Code** with GitHub Copilot Chat installed.
3. **Verify it loaded:** in the Chat view, open the settings gear → **Instructions & Rules** should list your instruction files; the `/agents` picker should list Planner, Implementer, Reviewer, and Software Engineer.
4. **Try a workflow:**
   - Select the **Planner** agent, describe a change → get a plan → click **Start Implementation** (handoff) to switch to the Implementer.
   - Or, describe a multi-day project — the AI will invoke the **project-delivery** skill and walk you through the phases.
   - For a bug fix or a small feature, ask for a "quick task" and the **quick-task** skill will kick in.
5. **Customise:**
   - Add or update files under `.github/instructions/` for your stack.
   - Edit `AGENTS.md` with any repo-specific standards.
   - Add MCP servers to `.vscode/mcp.json` for internal APIs, databases, or docs.

## Handoff flow (demo-friendly)

```mermaid
flowchart LR
    User([User prompt])
    User --> SE[Software Engineer]
    SE -->|Plan First| Planner
    Planner -->|Start Implementation| Implementer
    Implementer -->|Send for Review| Reviewer
    Reviewer -->|Back to Implementer| Implementer
```

Each handoff renders as a button under the chat response. No copy-paste of `#file:` references.

## Skills vs prompts — quick reference

**Use a skill** for multi-step deterministic workflows (like project-delivery, quick-task). The AI reads the skill once and executes it.

**Use a prompt file** (`.github/prompts/*.prompt.md`, not included in this template) for one-shot repeatable tasks — "scaffold a React component", "generate a security review". Prompts are invoked via `/prompt-name` slash commands and support YAML frontmatter for `agent`, `model`, `tools`, `description`.

## References

- [VS Code — Agent customization overview](https://code.visualstudio.com/docs/agent-customization/overview)
- [VS Code — Custom agents](https://code.visualstudio.com/docs/agent-customization/custom-agents)
- [VS Code — Agent skills](https://code.visualstudio.com/docs/agent-customization/agent-skills)
- [VS Code — Custom instructions](https://code.visualstudio.com/docs/agent-customization/custom-instructions)
- [VS Code — MCP servers](https://code.visualstudio.com/docs/agent-customization/mcp-servers)
- [`AGENTS.md` convention](https://agents.md)
- [awesome-copilot](https://github.com/github/awesome-copilot) — community-contributed examples

## Contributing

Additions welcome — new instructions files, new agents, new skills. Keep to the format described in each folder's siblings and the linked VS Code docs.

## License

[MIT](LICENSE).
