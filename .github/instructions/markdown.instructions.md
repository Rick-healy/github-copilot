---
description: 'Markdown writing conventions for docs, READMEs, and instruction files'
applyTo: '**/*.md'
---

# Markdown

## Structure

- One `#` H1 per file (the title).
- Don't skip heading levels (`#` → `##` → `###`, never `#` → `###`).
- Add a table of contents only for documents longer than ~5 sections. Otherwise the heading outline in the editor is enough.

## Style

- Plain English. Prefer short sentences over comma-chained ones.
- Sentence case for headings, not Title Case ("Testing standards", not "Testing Standards").
- Prefer active voice ("run the tests" not "the tests should be run").
- One idea per paragraph. Blank line between paragraphs.

## Lists

- Use `-` for unordered lists (consistent across the repo).
- Numbered lists only when order matters.
- Nested lists use two-space indentation.
- Terminate list items with a period only if they are full sentences.

## Code

- Fenced code blocks with a language tag: ` ```ts `, ` ```bash `, ` ```yaml `.
- Inline code for symbol names, filenames, and short commands: `myFunction`, `package.json`.
- Never wrap file paths in backticks *and* also make them a link — pick one (link preferred, so they're clickable).

## Links

- Relative links for anything inside the repo: `[README](../README.md)`.
- Absolute HTTPS links for external references.
- Descriptive link text; never "click here" or bare URLs in prose.

## Tables

- Include a header separator row.
- Align columns for readability if the table is short; don't stress about it in wide tables.

## Frontmatter (`.instructions.md`, `.prompt.md`, `.agent.md`)

- YAML frontmatter goes at the very top of the file, delimited by `---`.
- Fields on their own lines. Quote strings that contain colons, brackets, or leading numbers.
- Keep frontmatter fields to the ones the format supports (see [`AGENTS.md`](../../AGENTS.md) references).

## Length

- If a `.md` file exceeds ~500 lines, consider splitting it. Long files are harder to review and harder for the AI to load into context.
