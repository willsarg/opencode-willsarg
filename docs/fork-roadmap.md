# OpenCode Fork Roadmap

This file tracks intentional divergence from upstream so future sessions can tell the difference between planned fork work and accidental drift.

## Current Priorities

- Fix provider accounting correctness.
- Fix OpenRouter cost, token, and cache attribution.
- Make provider usage reporting trustworthy enough to act on.
- Keep fork-only workflow and documentation lightweight.

## Preferred Fork Shape

- Keep structural drift low.
- Favor small targeted patches over broad rewrites.
- Keep Will-specific UX or defaults separate from upstreamable bug fixes.

## Good Candidates For Upstream

- Generic provider accounting fixes
- OpenRouter usage and cost correctness fixes
- Bug fixes that improve accuracy without adding Will-specific product opinions

## Fork-Only Candidates

- Personal workflow defaults
- Local setup conventions
- Docs that exist only to guide future sessions in this fork

