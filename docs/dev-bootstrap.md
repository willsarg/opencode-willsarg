# OpenCode Dev Bootstrap

## Package Manager Preference

Use Homebrew first when installing machine-level tooling. Use npm only if Homebrew is not appropriate.

## Required Tooling

Install Bun with Homebrew:

```bash
brew install bun
```

Optional but useful:

```bash
brew install git
brew install gh
```

## Install Dependencies

From the repository root:

```bash
bun install
```

## Common Commands

From the repository root:

```bash
bun lint
bun typecheck
bun run dev
bun run dev:web
bun run dev:console
bun run dev:desktop
```

## Testing

- Do not run `bun test` from the repo root. It is intentionally blocked.
- Run tests from the relevant package directory only.
- Prefer the narrowest package-level test command that matches the files you changed.

Example:

```bash
bun run --cwd packages/opencode test
```

## Branch Reminder

For normal fork work:

- `dev` is the default branch
- `feat/*` branches are temporary
- `main` is only the upstream-sync mirror

See `docs/willsarg-fork-workflow.md` for the full branch policy.

