---
name: electrobun-init
description: This skill should be used when the user asks to "electrobun init", "scaffold a new electrobun project", "new electrobun app", "electrobun template", or "start electrobun project". Activates on new project setup or template questions.
version: 1.0.0
---

# Electrobun Init

Scaffolds a new Electrobun desktop app from a built-in template.

## Commands

```bash
electrobun init                                    # interactive template picker
electrobun init <project-name>                     # pick template interactively, name set
electrobun init <template-name>                    # template name = project name
electrobun init <project-name> --template=<name>  # explicit name + template
```

After init:
```bash
cd <project-name>
bun install
bun start         # runs: electrobun dev
```

## Template Reference

Choose based on what your app primarily does:

### Minimal / Learning
| Template | Use when |
|---|---|
| `hello-world` | Starting from scratch, learning Electrobun |
| `bunny` | Demo/mascot, visual showcase |

### UI Framework Integration
| Template | Use when |
|---|---|
| `svelte` | You use Svelte |
| `vue` | You use Vue |
| `solid` | You use SolidJS |
| `angular` | You use Angular |
| `react-tailwind-vite` | React + Tailwind, Vite bundler |
| `tailwind-vanilla` | No framework, just Tailwind |
| `vanilla-vite` | No framework, Vite bundler |

### App Patterns
| Template | Use when |
|---|---|
| `multi-window` | App needs multiple independent windows |
| `tray-app` | Menu bar / system tray app, no main window |
| `notes-app` | CRUD app with local storage |
| `sqlite-crud` | App with a local SQLite database |
| `photo-booth` | Camera/media capture |

### Browser / Multi-tab
| Template | Use when |
|---|---|
| `multitab-browser` | Multi-tab browser shell with navigation |

### 3D / Graphics
| Template | Use when |
|---|---|
| `wgpu-threejs` | 3D scene with Three.js on native WebGPU |
| `wgpu` | Native GPU rendering with Dawn/WebGPU |
| `wgpu-mlp` | WebGPU neural net inference |
| `wgpu-babylon` | BabylonJS on WebGPU |

## What Gets Generated

Every template produces at minimum:
```
<project-name>/
├── package.json          # scripts: start, dev, build, build:canary
├── electrobun.config.ts  # app identity + build config
├── tsconfig.json
├── bun.lock
└── src/
    ├── bun/
    │   └── index.ts      # main process entry
    └── <viewname>/       # one or more renderer dirs
        ├── index.html
        ├── index.ts
        └── index.css
```

GPU templates (`wgpu`, `wgpu-mlp`, `wgpu-babylon`, `wgpu-threejs`) skip the renderer dirs and use `GpuWindow` directly.

## Post-Init Checklist

1. `cd <project-name> && bun install`
2. Update `app.name`, `app.identifier`, `app.version` in `electrobun.config.ts`
3. Run `bun start` to verify the template runs
4. For GPU templates: confirm `bundleWGPU: true` is set per platform
5. For CEF templates: confirm `bundleCEF: true` is set per platform
6. Commit the scaffold before making changes

## Common Issues

- **"Template not found"** → Run `electrobun init` with no args to see the interactive picker with valid names
- **`bun install` fails** → Ensure Bun ≥ 1.0. Run `bun --version`
- **App launches but blank window** → Check that the view name in `electrobun.config.ts` `views` matches the directory name in `src/`
