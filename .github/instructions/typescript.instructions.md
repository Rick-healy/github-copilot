---
description: 'TypeScript / JavaScript coding conventions'
applyTo: '**/*.{ts,tsx,js,jsx,mts,cts}'
---

# TypeScript / JavaScript

## General

- Prefer TypeScript over JavaScript for new code. If the project is JS-only, don't force a migration.
- Follow the existing project's linter/formatter config (`eslint.config.js`, `.prettierrc`, `biome.json`). Do not add new tooling without asking.
- Match the file's existing module system (ESM `import` / CJS `require`).

## Style

- Use `const` by default; `let` when reassignment is genuinely needed; never `var`.
- Prefer arrow functions for callbacks and functional utilities; use `function` declarations for top-level named exports.
- Prefer template literals over string concatenation.
- Prefer optional chaining (`?.`) and nullish coalescing (`??`) over manual guards.

## TypeScript specifics

- **Strict mode.** Assume `strict: true` in `tsconfig.json`. If it isn't enabled, prefer adding it in a follow-up PR rather than working around it.
- **No `any`.** Use `unknown` and narrow, or a proper type. `// @ts-ignore` and `// @ts-expect-error` require a comment explaining why.
- **Types vs interfaces.** Use `interface` for object shapes that may be extended; `type` for unions, mapped types, and utility types.
- **Discriminated unions** for state that has finite shapes (e.g. `{ status: 'loading' } | { status: 'error', error: E } | { status: 'ready', data: D }`).
- **`readonly`** for props, config, and anything that shouldn't mutate after creation.
- **No enums.** Prefer `as const` object literals or string-literal union types.

## Async

- `async/await` for readability; `Promise.all` for concurrent independent work.
- Never leave a Promise unawaited without an explicit `void` or `.catch()`.
- No floating promises in loops — use `for...of` with `await` or `Promise.all(items.map(...))`.

## Errors

- Throw `Error` (or a subclass), never strings or plain objects.
- Catch narrowly. `catch (e) { if (e instanceof FooError) ... }`. Don't swallow errors silently.

## Testing

- Test files sit next to the source (`foo.ts` + `foo.test.ts`) or in a mirrored `__tests__/` folder, whichever the project already does.
- Use whichever framework is already installed (`vitest`, `jest`, `node:test`). Don't introduce a new one.
- Test names describe behaviour: `it('returns null when the input is empty')`, not `it('test1')`.

## Bundling / imports

- Import types explicitly with `import type { … }` where the linter enforces it.
- Prefer named exports over default exports.
- No barrel files (`index.ts` re-exporting everything) unless the project is already built around them — they hurt tree-shaking.
