# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

LevelX — *Shardfall Expedition*: a 2D action-platformer RPG shipped as a single HTML file. No build step, no framework, no npm. Open `maple_game.html` in a browser to play.

## Commands

- **Run the game**: open `maple_game.html` directly in a browser. Refresh to pick up edits.
- **Syntax check before committing**: extract the `<script>` block from `maple_game.html` into a temp `.js` and run `node --check`. The script block starts at the `<script>` tag near line 915. There are no tests or linters in this repo — `node --check` is the only automated gate.
- **Sprite tool**: open `sprite_maker.html` directly in a browser. It is independent of the game.
- **Preview URL after push**: `https://raw.githack.com/dpeh001-x/LevelX/preview/maple_game.html` (see "Preview branch" below).
- **Live URL (GitHub Pages, main branch)**: `https://dpeh001-x.github.io/LevelX/maple_game.html`.

## Architecture

`maple_game.html` (~14k lines) is the entire game: inline CSS, HTML skeleton, and one `<script>` block. The script is organized by `// ===` banner comments; grep for those to navigate. Top-level anchors:

- `GAME_VERSION` constant near line 920 — **bump on every release**; it renders in the top-right HUD.
- `const SKILLS` (~line 970) — flat dictionary of every skill keyed by id, with `cls`, `job`, `slot` (keybind), `mp`, `cd`, `desc`. Master signatures live at the bottom of this block.
- Skill-tree branches (`// ===== <Job> branches =====`) — the 24 passive nodes across 8 jobs.
- Canvas/core, input, physics, combat, HUD, modals, map transitions, enemy AI, loot/affixes, star enhancement, and save/load are each their own banner-comment section.

There is no asset pipeline in the running game: character sprites are drawn procedurally on canvas by compositing base body + hair/armor/cape/weapon/helmet/shield/boots layers. The `Sprites/` folder and the `*_prompts.md` / `*_specs.md` files are **authoring notes for sprite generation**, not runtime assets.

Separate files:
- `CHANGELOG.html` — canonical, human-facing changelog (see policy below). **There is no `CHANGELOG.md`.**
- `GAMING_AUDIT.html` — shareable design/psychology audit document.
- `sprite_maker.html` — standalone in-browser sprite preview/export tool.

## Changelog policy (durable)

**Always update `CHANGELOG.html` whenever a new implementation lands.**

- Add an entry under the correct section (or create a new section) describing what shipped.
- Keep the HTML self-contained — inline CSS, no external deps. It is shared directly via `raw.githack` / `blob` URLs, so it must render as a standalone document.
- Match the existing styling: dark theme, pill components, `<kbd>` for keys, summary cards, callouts, controls table.
- When a keybind or feature is renamed, update **every** reference — summary table, controls table, verification checklist, inline mentions.
- Do not create a `CHANGELOG.md` alongside. `CHANGELOG.html` is the only canonical artifact.

## Branch & shipping workflow

- Develop on the feature branch specified in environment instructions.
- Fast-forward `main` from the feature branch only when the user explicitly asks to push to main. Never force-push `main`.
- **Preview branch**: a dedicated `preview` branch holds whatever build is currently under review so reviewers bookmark the same URL.
  - Only update `preview` when the user explicitly asks (e.g. "push a preview"). Not automatic on every push.
  - `preview` is force-overwritten each time — safe to force-push with lease; it carries no durable history.
  - Fast-forward `preview` from the current branch tip (or a chosen commit), not via merge commits.

## Version bumps

Release commits update `GAME_VERSION` in `maple_game.html` and add a matching entry to `CHANGELOG.html`. Follow the existing versioning rhythm visible in `git log` (e.g. `v0.11.1` → `v0.11.2` for tuning, `v0.11` → `v0.12` for feature drops).
