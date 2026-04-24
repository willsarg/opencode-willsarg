- This repository is Will's fork of `sst/opencode`.
- Primary goal: fix provider/accounting issues and make targeted product improvements without making upstream sync painful.
- To regenerate the JavaScript SDK, run `./packages/sdk/js/script/build.ts`.
- ALWAYS USE PARALLEL TOOLS WHEN APPLICABLE.
- The default branch in this repo is `dev`.
- Use `dev` for active work. `main` exists only as the upstream-sync mirror.
- Prefer automation: execute requested actions without confirmation unless blocked by missing info or safety/irreversibility.

## Fork Workflow

- `main` is the upstream-sync branch and should remain a clean mirror of `sst/opencode`.
- `dev` is Will's integration branch and the default branch for active fork work.
- `feat/*` branches are temporary scoped task branches from `dev`.
- Never do feature work directly on `main`.
- Merge finished `feat/*` work into `dev` immediately after verification, then delete the feature branch locally and on origin.
- If a task is tiny, docs-only, or strictly local, a narrowly scoped commit on `dev` is acceptable.

## First Steps For Each Non-Trivial Task

1. Run `git status -sb`.
2. Run `git branch --show-current`.
3. Read this file and `docs/willsarg-fork-workflow.md`.
4. If toolchain setup may be needed, read `docs/dev-bootstrap.md`.
5. For substantial work, create a new `feat/*` branch from `dev`.

## Fork Maintainability Rules

- Keep commits and branches tightly scoped.
- Keep Will-specific changes separate from upstreamable fixes when practical.
- Avoid large mixed-purpose refactors unless they are clearly worth the future merge cost.
- Before changing structure, ask: "Can this be implemented as a small patch on top of upstream?"
- If yes, do that.
- If no, document the reason in the commit message, PR notes, or task notes.

## Style Guide

### General Principles

- Keep things in one function unless composable or reusable
- Avoid `try`/`catch` where possible
- Avoid using the `any` type
- Use Bun APIs when possible, like `Bun.file()`
- Rely on type inference when possible; avoid explicit type annotations or interfaces unless necessary for exports or clarity
- Prefer functional array methods (flatMap, filter, map) over for loops; use type guards on filter to maintain type inference downstream
- In `src/config`, follow the existing self-export pattern at the top of the file (for example `export * as ConfigAgent from "./agent"`) when adding a new config module.

Reduce total variable count by inlining when a value is only used once.

```ts
// Good
const journal = await Bun.file(path.join(dir, "journal.json")).json()

// Bad
const journalPath = path.join(dir, "journal.json")
const journal = await Bun.file(journalPath).json()
```

### Destructuring

Avoid unnecessary destructuring. Use dot notation to preserve context.

```ts
// Good
obj.a
obj.b

// Bad
const { a, b } = obj
```

### Variables

Prefer `const` over `let`. Use ternaries or early returns instead of reassignment.

```ts
// Good
const foo = condition ? 1 : 2

// Bad
let foo
if (condition) foo = 1
else foo = 2
```

### Control Flow

Avoid `else` statements. Prefer early returns.

```ts
// Good
function foo() {
  if (condition) return 1
  return 2
}

// Bad
function foo() {
  if (condition) return 1
  else return 2
}
```

### Schema Definitions (Drizzle)

Use snake_case for field names so column names don't need to be redefined as strings.

```ts
// Good
const table = sqliteTable("session", {
  id: text().primaryKey(),
  project_id: text().notNull(),
  created_at: integer().notNull(),
})

// Bad
const table = sqliteTable("session", {
  id: text("id").primaryKey(),
  projectID: text("project_id").notNull(),
  createdAt: integer("created_at").notNull(),
})
```

## Testing

- Avoid mocks as much as possible
- Test actual implementation, do not duplicate logic into tests
- Tests cannot run from repo root (guard: `do-not-run-tests-from-root`); run from package dirs like `packages/opencode`.

## Type Checking

- Always run `bun typecheck` from package directories (e.g., `packages/opencode`), never `tsc` directly.
