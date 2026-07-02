---
description: 'C# / .NET coding conventions'
applyTo: '**/*.{cs,csx}'
---

# C# / .NET

## Target framework

- Prefer modern .NET (currently .NET 9+). If the project targets .NET Framework, don't propose migration mid-task.
- Enable **`Nullable`** (`<Nullable>enable</Nullable>`) and treat warnings as errors where feasible.
- Enable **implicit usings** in modern projects.

## Style

- Follow [.NET runtime coding style](https://github.com/dotnet/runtime/blob/main/docs/coding-guidelines/coding-style.md) and let `.editorconfig` / `dotnet format` enforce it.
- File-scoped namespaces (`namespace Foo;`) over block-scoped.
- One public type per file; filename matches the type name.

## Naming

- `PascalCase` for types, methods, properties, events, public fields.
- `camelCase` for locals and parameters.
- `_camelCase` for private instance fields; `s_camelCase` for private static; `t_camelCase` for thread-static.
- `UPPER_SNAKE_CASE` is not used in idiomatic C#; use `PascalCase` for constants.
- Interfaces are `IFoo`. Async methods end with `Async`. Cancellation tokens are named `cancellationToken`.

## Language features

- Use `record` (or `record struct`) for immutable data carriers.
- Use `required` members over positional constructors when a type has more than a couple of properties.
- Use collection expressions (`[1, 2, 3]`) and primary constructors where they fit.
- Use `switch` expressions and pattern matching over cascading `if`.
- Use `var` when the type is obvious from the right-hand side.
- Use `System.Text.Json` over `Newtonsoft.Json` unless the project already uses the latter.

## Async

- `async Task` / `async Task<T>` for asynchronous methods. `async void` only for event handlers.
- Accept and forward `CancellationToken` on any operation that could be long-running or I/O bound.
- Use `ConfigureAwait(false)` in library code; not needed in ASP.NET Core / modern app code where there's no sync context.
- `await` in a `foreach` uses `IAsyncEnumerable<T>` — prefer streaming APIs over `List<T>` materialization when the caller might not need everything.

## Errors

- Throw specific exception types (`ArgumentNullException.ThrowIfNull(...)`). Don't catch and rethrow just to add context — use `throw new XxxException("context", inner)`.
- Don't catch `Exception` unless you're at a system boundary and you re-throw or log responsibly.
- Prefer `TryXxx(out …)` patterns over exception-based control flow for expected failure modes.

## Dependency injection

- Register dependencies in `Program.cs` (or a dedicated extension method on `IServiceCollection`). Prefer constructor injection.
- Scope: `Transient` for stateless, `Scoped` for per-request (web), `Singleton` only for thread-safe stateless services.

## Testing

- One test project per test type per production project (`Foo.Tests`, `Foo.IntegrationTests`).
- Use whichever framework the project uses (`xUnit`, `NUnit`, `MSTest`). Don't switch.
- Test names: `Method_Scenario_ExpectedBehavior` (xUnit convention) or `Should_do_x_when_y` — match what's already there.
- Use `FluentAssertions` if the project does; otherwise stick with the framework's assertions.

## Performance (when it matters)

- Prefer `Span<T>` / `Memory<T>` / `ReadOnlySpan<T>` in hot paths. Don't apply these speculatively.
- Use `ArrayPool<T>` and `StringBuilder` when profiling shows allocation pressure.
- Consult the `analyzing-dotnet-performance` skill if profiling / benchmark work is needed.
