# Design: Electrobun CLI Skills & Workflows Extension

**Date:** 2026-03-10
**Status:** Approved
**Extends:** `~/.claude/plugins/electrobun-dev/`

## Overview

Add lifecycle-layered CLI skills, a master workflow skill, and three commands to the existing `electrobun-dev` plugin. Covers the full `init → dev → build → release` pipeline with deep knowledge of every flag, env var, template, config field, and artifact.

## New Components

### Skills (6)

| Skill directory | Trigger context | Domain |
|---|---|---|
| `skills/electrobun-init/` | New project setup, template selection | 18 templates, post-init steps |
| `skills/electrobun-dev/` | `electrobun dev`, watch mode, hot reload | `--watch`, debounce, CEF devtools on :9222 |
| `skills/electrobun-build/` | Build flags, signing, notarization | 3 envs, env vars, build step sequence, artifacts |
| `skills/electrobun-release/` | Release, auto-updates, bsdiff, upload | update.json, patch strategy, Updater API |
| `skills/electrobun-config/` | Editing `electrobun.config.ts` | Complete typed field reference with defaults |
| `skills/electrobun-workflow/` | Any Electrobun project | Full pipeline map, stage transitions, cross-stage gotchas |

### Commands (3 new + 1 extended)

| Command | Purpose |
|---|---|
| `commands/electrobun-workflow.md` | Pipeline status + jump-start any stage |
| `commands/electrobun-build.md` | Guided env-aware build with artifact summary |
| `commands/electrobun-release.md` | Step-by-step release: version bump → build → upload → verify |
| `commands/electrobun-init.md` | **Extended** with full 18-template picker |

## CLI Surface (from exploration)

### Subcommands
- `electrobun init [name] [--template=<name>]`
- `electrobun dev [--watch]`
- `electrobun build [--env=dev|canary|stable]`
- `electrobun run`

### 18 Templates
hello-world, photo-booth, multi-window, svelte, tailwind-vanilla, three-physics, vue, wgpu, tray-app, bunny, angular, solid, sqlite-crud, react-tailwind-vite, wgpu-mlp, webgpu-babylon, vanilla-vite, notes-app

### Build Environments
| Env | Signs | Notarizes | DMG | bsdiff patch |
|---|---|---|---|---|
| dev | No | No | No | No |
| canary | Yes | Yes | Yes | Yes |
| stable | Yes | Yes | Yes | Yes |

### Env Vars (signing)
- `ELECTROBUN_DEVELOPER_ID` — code signing identity
- `ELECTROBUN_APPLEID` — notarization Apple ID
- `ELECTROBUN_APPLEIDPASS` — app-specific password
- `ELECTROBUN_TEAMID` — Apple team ID
- `ELECTROBUN_SKIP_CODESIGN=1` — skip signing even if configured

### Build Hook Env Vars (injected into scripts)
`ELECTROBUN_BUILD_ENV`, `ELECTROBUN_OS`, `ELECTROBUN_ARCH`, `ELECTROBUN_BUILD_DIR`, `ELECTROBUN_APP_NAME`, `ELECTROBUN_APP_VERSION`, `ELECTROBUN_APP_IDENTIFIER`, `ELECTROBUN_ARTIFACT_DIR`

### Artifact Outputs (canary/stable)
- `artifacts/<platform>-<arch>-<env>-<version>.tar.zst` — full compressed bundle
- `artifacts/<platform>-<arch>-<env>-<version>.patch` — bsdiff delta patch
- `artifacts/<platform>-<arch>-update.json` — manifest for auto-updater
- `artifacts/<platform>-<arch>-<env>-<version>.dmg` — macOS installer

### Config Fields Added to Skill
Complete `electrobun.config.ts` schema including `build.cefVersion`, `build.wgpuVersion`, `build.bunVersion`, `build.useAsar`, `build.asarUnpack`, `build.locales`, `build.watch`, `build.watchIgnore`, `build.artifactFolder`, `build.buildFolder`, all platform `chromiumFlags`, all `entitlements`.

## Workflow Skill Design

The `electrobun-workflow` skill links stages via explicit "→ next" pointers:

```
Stage 1: INIT        → electrobun-init skill
Stage 2: DEVELOP     → electrobun-dev skill
Stage 3: BUILD       → electrobun-build skill + /electrobun-build command
Stage 4: RELEASE     → electrobun-release skill + /electrobun-release command
```

Each stage section notes:
- What command(s) to run
- What success looks like
- Common failure modes
- When you're ready to advance to the next stage

## File Changes

**Add to `~/.claude/plugins/electrobun-dev/`:**
```
skills/
├── electrobun-init/SKILL.md       (new)
├── electrobun-dev/SKILL.md        (new)
├── electrobun-build/SKILL.md      (new)
├── electrobun-release/SKILL.md    (new)
├── electrobun-config/SKILL.md     (new)
└── electrobun-workflow/SKILL.md   (new)
commands/
├── electrobun-workflow.md         (new)
├── electrobun-build.md            (new)
├── electrobun-release.md          (extended — replace existing)
└── electrobun-init.md             (extended — add 18-template picker)
```
