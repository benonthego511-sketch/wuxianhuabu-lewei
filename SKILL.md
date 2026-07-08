---
name: infinite-canvas-image-skill
description: Build, package, debug, and maintain the Lewei infinite canvas image generation tool. Use when working on the local web app, CLI packaging, multi-reference image generation, role-based canvas edges, product image isolation, provider adapters, one-click startup, or npm-style installation for the infinite canvas tool.
---

# Infinite canvas image skill

Use this skill when modifying or packaging the Lewei infinite canvas image generation tool.

The goal is a local Web App that users can install and start without understanding the repository internals:

```bash
npm install -g infinite-canvas-image-skill
infinite-canvas start
```

The app should open a browser at `http://localhost:3000` by default and expose a health endpoint at `/api/health`.

## Core principles

- Treat the canvas as an orchestration layer. Generation parameters must come from the node graph, not from incidental UI state.
- Never let a canvas image participate in generation just because it exists on the canvas.
- A GenerateNode may accept many independent image references.
- Every image reference must keep its own role, weight, instruction, enabled state, and order.
- Do not merge multiple reference images into one composite image before generation.
- Product images must not automatically affect scene, pose, lighting, composition, or style.
- The final generation payload must be inspectable and debuggable.
- Keep Windows and macOS support first-class. Do not hardcode absolute local paths.

## Current source locations

When working in the original monorepo, start here:

- UI: `apps/examples/src/examples/use-cases/ai-canvas-agent/AiCanvasAgentExample.tsx`
- CSS: `apps/examples/src/examples/use-cases/ai-canvas-agent/ai-canvas-agent.css`
- Logo: `apps/examples/src/examples/use-cases/ai-canvas-agent/assets/logo.svg`
- Local API and generation middleware: `apps/examples/vite.config.ts`
- Existing one-click startup helpers: `open-ai-canvas.cmd`, `open-ai-canvas.vbs`, `start-dev.cmd`, `verify-ai-canvas-local.ps1`

Search these terms before editing generation behavior:

```text
reference
references
imageUrl
inputImage
sourceImage
GenerateNode
edges
targetHandle
handle
payload
generateImage
generationPayload
buildGenerationPayload
```

## Required package shape

The packaged tool should live under:

```text
packages/infinite-canvas-image-skill/
```

Recommended structure:

```text
packages/infinite-canvas-image-skill/
  package.json
  README.md
  skill.json
  bin/infinite-canvas.js
  src/cli/start.ts
  src/cli/doctor.ts
  src/cli/config.ts
  src/server/index.ts
  src/server/routes/health.ts
  src/server/routes/generate.ts
  src/server/routes/canvas.ts
  src/server/routes/assets.ts
  src/server/routes/history.ts
  src/core/referenceRoles.ts
  src/core/nodeTypes.ts
  src/core/edgeTypes.ts
  src/core/graph.ts
  src/core/buildGenerationPayload.ts
  src/core/validateGraph.ts
  src/core/productIsolation.ts
  src/core/storage.ts
  src/adapters/index.ts
  src/adapters/customAdapter.ts
  src/adapters/openaiAdapter.ts
  src/adapters/googleAdapter.ts
  src/adapters/liblibAdapter.ts
  public/logo.svg
  examples/demo-canvas.json
  docs/api.md
```

## CLI requirements

Provide these commands:

```bash
infinite-canvas start
infinite-canvas start --port 3001
infinite-canvas doctor
infinite-canvas config
```

Behavior:

- `start` creates storage directories on first run, starts the local server, and opens the browser.
- `--port` overrides the default port.
- `doctor` checks Node version, storage paths, port availability, config readability, adapter readiness, and API key presence.
- `config` prints the active config location and safe editable fields. Never print secret values.

## Provider adapters

Implement adapters behind a shared interface:

- `customAdapter`: arbitrary HTTP gateway, suitable for AI charging station, Beeroute/Lingya, and other OpenAI-compatible gateways.
- `openaiAdapter`: reads `OPENAI_API_KEY`.
- `googleAdapter`: reads Google/Gemini API key.
- `liblibAdapter`: reserved adapter, do not hardcode credentials.

Configuration priority:

1. CLI args
2. environment variables
3. user config file
4. defaults

## Validation checklist

Before claiming success, verify:

- `infinite-canvas doctor` runs.
- `infinite-canvas start --port 3000` starts the app.
- `/api/health` returns ok.
- Browser opens the canvas.
- Uploading multiple images creates independent image nodes.
- Multiple image nodes can connect to one GenerateNode.
- Connecting a second or third image does not replace the first reference.
- `generationPayload.references` is an array.
- Product references do not affect scene, pose, lighting, composition, or style.
- Deleting one edge removes only its matching reference.
- No API key state still allows the UI to open with a clear configuration prompt.
- Import/export works.
- The logo remains the Lewei SVG logo.

## Additional references

Read only what is needed:

- `references/implementation-plan.md` for the recommended migration sequence.
- `references/data-contracts.md` for TypeScript contracts and validation rules.
- `references/api-contract.md` for server routes and response shapes.
