# WillSarg OpenCode Fork Workflow

## Branch Model

- `upstream/dev` = original OpenCode project
- `main` = local upstream-sync mirror of `upstream/dev`
- `dev` = Will's active fork branch
- `feat/*` = short-lived task branches from `dev`

## Rules

- Keep commits and branches tightly scoped.
- Do not leave finished work on feature branches.
- Merge completed `feat/*` branches into `dev` after verification, then delete them locally and on origin.
- Keep Will-specific fork changes separate from upstreamable fixes when practical.

## Start New Work

```bash
git checkout dev
git pull --ff-only origin dev
git checkout -b feat/<short-name>
```

## Finish Work

Run the narrowest relevant verification for the area you changed.

Common checks:

```bash
bun lint
bun typecheck
```

Testing rules:

- Never run `bun test` from the repo root.
- Run tests from the relevant package directory, for example `bun run --cwd packages/opencode test`.

Then:

```bash
git status -sb
git add .
git commit -m "feat(...): ..."
git checkout dev
git merge --ff-only feat/<short-name>
git push origin dev
git branch -d feat/<short-name>
git push origin --delete feat/<short-name>
```

## Sync Upstream

```bash
git fetch upstream
git checkout main
git merge --ff-only upstream/dev
git push origin main

git checkout dev
git merge main
git push origin dev
```

## Upstreamable Fixes

If part of a change could reasonably go upstream:

1. Branch from `main`.
2. Keep the patch small and generic.
3. Exclude Will-specific branding, defaults, or workflow opinions.
4. Open a separate upstream PR only for that slice.

## Fresh Session Prompt

```text
Work in this repository and follow AGENTS.md, docs/dev-bootstrap.md, and docs/willsarg-fork-workflow.md before making changes. Keep branches and commits tightly scoped, merge finished work into dev, and delete merged feature branches.
```

