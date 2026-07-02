# 0.archive — Superseded customization files

These files were moved out of `.github/` during the 2026 migration documented in [../UPDATES-2026.md](../UPDATES-2026.md). They are kept for reference only.

## What was archived and why

| Original location | Replaced by | Reason |
|---|---|---|
| `.github/chatmodes/SoftwareEngineer.chatmode.md` | `.github/agents/*.agent.md` | Chat modes are no longer a top-level customization type in VS Code. Custom agents supersede them with handoffs, subagents, model fallback lists, and a first-class `/agents` picker. |
| `.github/prompts/project-delivery-orchestrator.prompt.md` + `.github/prompts/project-delivery/*` | `.github/skills/project-delivery/SKILL.md` | Multi-step orchestrated workflows are what **agent skills** are designed for. A single skill file replaces the orchestrator + 5 phase prompts + `#file:` chaining. |
| `.github/prompts/quick-task-orchestrator.prompt.md` + `.github/prompts/quick-task/*` | `.github/skills/quick-task/SKILL.md` | Same reason as above — a 4-phase workflow is a natural skill. |

## Do not use these files

They contain legacy patterns that no longer work as intended:

- `model: claude-4` — not a valid model id
- Bare tool names like `openSimpleBrowser`, `new`, `runNotebooks`, `searchResults` — renamed or removed
- `#file:...` cross-references — replaced by slash commands and Markdown links
- No YAML frontmatter on prompt files — required for the modern `/` picker

## Deleting this folder

Once you're satisfied with the new setup and don't need the reference, `0.archive/` can be deleted entirely.
