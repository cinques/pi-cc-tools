# AGENTS.md

## Fork layout

- `origin` = upstream `FammasMaz/pi-cc-tools` (npm: `pi-claude-code-ui`). Never push there.
- `fork` = `cinques/pi-cc-tools` — all work pushes here, to `master`.
- Keep `master` as a single squashed commit on top of upstream `master`. Force-push to `fork` with `--force-with-lease` is the norm. Keep the pre-squash chain on a local `backup/*` branch, not on the remote.

## This working copy is not what pi runs

pi loads this extension as a git package (listed in `~/.pi/agent/settings.json` → `packages`), cloned into its own cache at `~/.pi/agent/git/github.com/cinques/pi-cc-tools`. Committing here changes nothing visible until the full cycle runs:

1. commit → `git push fork master`
2. `pi update git:github.com/cinques/pi-cc-tools@master`
3. the user restarts pi

When the user reports "nothing changed", check this cycle first — compare the cache clone's HEAD with local `master`.

## Conventions

- Do not edit `CHANGELOG.md` — the owner keeps it identical to upstream.
- Bump `package.json` version (patch) on user-visible changes; mention the bump in the commit message.
- `npm run typecheck` and `npm test` must pass before committing.

## Rendering invariants

- Status dots are `⏺` (U+23FA) — the glyph Claude Code uses on macOS; it optically centers on the text line. `●` (U+25CF) renders baseline-sunk in terminal fonts and reads as misaligned. `visibleWidth('⏺') === 1`, so width/wrap/padding math is unaffected.
- Line markers: `CLIP_MARK` truncates a line at terminal width, `WRAP_MARK` wraps it. Tool output lines and branch leads clip; the long Bash header wraps, with continuation indentation handled by `markedContinuationPrefix`.
- `branchLead` pads 2 spaces so `└` sits under the header text (column 2), matching Claude Code; `branchIndent` aligns continuation content under that column.
