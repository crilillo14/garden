# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project intent

This is a personal fork of [Quartz v5](https://quartz.jzhao.xyz/) used to publish an Obsidian vault (symlinked at `content/` → an external vault directory) as a static digital-garden site. **The goal for work in this repo is aesthetic/theming customization only** — colors, fonts, spacing, layout of existing components. Do not add new plugins, components, or build-pipeline functionality unless explicitly asked; prefer the smallest CSS/config change that achieves the visual result.

## Commands

```bash
npx quartz build --serve      # local dev server with hot reload (build.ts watches .ts/.tsx/.scss and content/*.md)
npm run docs                  # build docs site (used for docs/, not content/)
npm run check                 # tsc --noEmit + prettier --check (run before committing)
npm run format                 # prettier --write
npm test                       # tsx --test — runs *.test.ts files under quartz/
tsx --test quartz/util/path.test.ts   # run a single test file
```

Plugin management (this fork's plugins are external git repos, not local code — see Architecture):

```bash
npx quartz plugin add github:quartz-community/<name>   # install a community plugin
npx quartz plugin install --latest                      # update all plugins to latest commits
npx quartz plugin install --clean                       # restore plugins pinned in quartz.lock.json (CI)
npx quartz plugin remove <name>
```

`npm run prebuild` (runs automatically before `build`) calls `install-plugins` to sync `.quartz/plugins/` with `quartz.lock.json`.

## Architecture

**Config-driven, not code-driven.** Almost everything about how the site looks and behaves is declared in `quartz.config.yaml`, not in `quartz.ts` (which just loads the config — see `quartz/plugins/loader/config-loader.ts`). Two things live there:
- `configuration:` — site-wide settings, including the `theme:` block (fonts, light/dark color palette).
- `plugins:` — an ordered list of plugins, each with a `source:` (usually `github:quartz-community/<name>`), `enabled`, `options`, and (for UI plugins) a `layout:` block controlling sidebar position/priority.

**Plugins are external, cloned repos, not source in this tree.** Quartz v5 (this fork) pulls community plugins from git into `.quartz/plugins/<name>/` (git-ignored, restored via `quartz.lock.json`) and re-exports them through an auto-generated `.quartz/plugins/index.ts`. Do not edit files under `.quartz/plugins/` expecting them to persist — treat them as installed dependencies, not editable source. If a plugin's built-in styling needs a tweak, override it from `quartz/styles/custom.scss` instead of patching the plugin.

Shared code is layered as `@quartz-community/types` (no deps) → `@quartz-community/utils` → `@quartz-community/runtime` (browser-only) → plugins (npm deps, not in this repo).

**Where styling actually lives (most relevant for this project's goals):**
- `quartz.config.yaml` → `configuration.theme` — typography (Google Fonts names) and the `lightMode`/`darkMode` color palettes (`light`, `lightgray`, `gray`, `darkgray`, `dark`, `secondary`, `tertiary`, `highlight`, `textHighlight`). Change colors/fonts here first.
- `quartz/styles/custom.scss` — the intended place for personal CSS overrides; currently empty aside from importing variables.
- `quartz/styles/variables.scss` — layout breakpoints (`$mobile`/`$tablet`/`$desktop`), grid templates for each breakpoint, side panel width, font weights.
- `quartz/styles/base.scss`, `callouts.scss`, `syntax.scss` — base/callout/code styling; `data-frame` attribute on `.page` allows frame-specific overrides here.
- `quartz/components/styles/` — per-component SCSS (e.g. popovers).
- The `quartz-themes` community plugin (installed, `theme: default` in config) ships many alternate full theme presets as CSS under `.quartz/plugins/quartz-themes/extras/*.css` (catppuccin, nord, matrix, 80s-neon, etc.) — switching `options.theme` in `quartz.config.yaml` is the fast path to a whole new look without hand-writing CSS.

**Page frames** (`quartz/components/frames/`) control the inner HTML skeleton per page type (`DefaultFrame` = 3-column, `FullWidthFrame`, `MinimalFrame`). Frame choice is set via `layout.byPageType.<type>.template` in `quartz.config.yaml` or per-plugin; this is a layout-shape lever distinct from colors/fonts.

**Content pipeline (for context, rarely touched here):** `bootstrap-cli.mjs` transpiles Quartz with esbuild → `build.ts` globs `content/`, parses Markdown through a unified/remark/rehype pipeline (transformer plugins → filter plugins → emitter plugins), converts hast → JSX via Preact, renders to static HTML. Full trace: `docs/advanced/architecture.md`.

**Content directory:** `content/` is a symlink to an external Obsidian vault (not part of this repo's git history). `ignorePatterns` in `quartz.config.yaml` (`private`, `templates`, `.obsidian`) controls what's excluded from publishing.

## Conventions

- Prettier config (`.prettierrc`): no semicolons, 100 col width, trailing commas, 2-space tabs. `.canvas` files parse as JSON, `.base` files as YAML.
- Node >=22 (`.node-version` pins 22.16.0), npm >=10.9.2.
