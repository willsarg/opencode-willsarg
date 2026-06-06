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

The upstream remote points to `https://github.com/anomalyco/opencode`. Their default branch is `dev`.

**1. Fetch and fast-forward `main`**

```bash
git fetch upstream
git checkout main
git merge --ff-only upstream/dev
git push origin main --no-verify
```

`--no-verify` skips the husky pre-push typecheck hook. This is intentional for `main`: it is a clean mirror of upstream, not our code, and upstream may introduce typecheck failures before their deps are installed.

**2. Rebase `dev` onto `main`**

```bash
git checkout dev
git rebase main
```

Expect conflicts in files where our fixes overlap with upstream changes (historically: `session/session.ts`, `provider/provider.ts`, `provider/models.ts`, `config/provider.ts`). Resolve by keeping upstream's structural changes and re-applying our logic on top.

After resolving each conflict file:

```bash
git add <file>
git rebase --continue
```

**3. Push the rebased `dev`**

```bash
git push origin dev --force-with-lease --no-verify
```

`--force-with-lease` is required because rebase rewrites commit hashes. `--no-verify` skips the typecheck hook — run typecheck separately afterward if needed.

**4. Verify**

```bash
# Our commits should be on top of main, nothing in main that isn't in dev
git log --oneline main..dev
git log --oneline dev..main  # should be empty
```

**What to check after each sync**

Our code commits live in `packages/opencode/src/`:

| Commit area | Files | What to verify |
|---|---|---|
| Cached input accounting | `session/session.ts` | `adjustedInputTokens` / `noCacheInputTokens` logic still present |
| OpenRouter direct cost | `session/session.ts` | `openrouterCost` short-circuit still present |
| OpenRouter token fallback | `session/session.ts` | `orUsage` fallback fields still in place |
| Reasoning token pricing | `provider/provider.ts`, `provider/models.ts`, `config/provider.ts`, `session/session.ts` | `reasoning` field in cost schemas; `costInfo?.reasoning` in cost calc |
| PWD working directory | `index.ts` | `existsSync` + `process.chdir` block still present |

If upstream has independently fixed any of these, our commit may become a no-op or conflict — review the diff and drop our commit if upstream's version is equivalent or better.

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
