# Implementation plan

## Current project scan

The current local tool is based on the tldraw examples app.

Important files in the source project:

- UI: `apps/examples/src/examples/use-cases/ai-canvas-agent/AiCanvasAgentExample.tsx`
- Styles: `apps/examples/src/examples/use-cases/ai-canvas-agent/ai-canvas-agent.css`
- Logo asset: `apps/examples/src/examples/use-cases/ai-canvas-agent/assets/logo.svg`
- Local API and generation middleware: `apps/examples/vite.config.ts`
- Startup helpers: `open-ai-canvas.cmd`, `open-ai-canvas.vbs`, `start-dev.cmd`, `verify-ai-canvas-local.ps1`

## Target outcome

Package the tool as an installable local Web App:

```bash
npm install -g infinite-canvas-image-skill
infinite-canvas start
```

Default browser URL:

```text
http://localhost:3000
```

## Phase 1: package skeleton

Create `packages/infinite-canvas-image-skill` with:

- `package.json`
- `skill.json`
- `bin/infinite-canvas.js`
- CLI modules for `start`, `doctor`, and `config`
- a minimal server with `GET /api/health`
- cross-platform storage initialization

Do not move all UI logic in this phase.

## Phase 2: extract core contracts

Move reusable generation contracts into `src/core`:

- `referenceRoles.ts`
- `nodeTypes.ts`
- `edgeTypes.ts`
- `buildGenerationPayload.ts`
- `validateGraph.ts`
- `productIsolation.ts`
- `storage.ts`

Keep the current example working while extracting logic.

## Phase 3: server APIs

Implement:

- `GET /api/health`
- `POST /api/generate`
- `POST /api/assets/upload`
- `POST /api/canvas/import`
- `POST /api/canvas/export`
- `GET /api/history`

Use `references/api-contract.md` as the route contract.

## Phase 4: provider adapters

Implement a shared adapter interface and these adapters:

- `customAdapter`
- `openaiAdapter`
- `googleAdapter`
- `liblibAdapter` as reserved placeholder

Never hardcode user API keys.

## Phase 5: UI packaging

Bundle the existing canvas UI into the package server.

Requirements:

- Keep the Lewei SVG logo.
- Keep the multi-reference GenerateNode UI.
- Keep role, weight, instruction, enabled, order controls.
- Keep generation payload inspection.
- Do not regress current local development startup.

## Phase 6: package and verify

Validation commands:

```bash
npm install -g ./packages/infinite-canvas-image-skill
infinite-canvas doctor
infinite-canvas start --port 3000
```

Expected result:

- Browser opens the canvas.
- `/api/health` returns ok.
- Multiple image references work without replacement.
- Generation payload contains independent `references` array entries.
- Import/export and history endpoints work.
- Windows and macOS paths are handled without hardcoded absolute paths.

## File migration strategy

Prefer incremental extraction:

1. Copy or extract stable contracts first.
2. Wire the package server without changing UI behavior.
3. Move API calls from `/api/generate-image` to `/api/generate` only after the new endpoint is verified.
4. Keep compatibility aliases during migration where safe.
5. Remove duplicated code only after tests pass.

## Risk controls

Avoid these mistakes:

- Creating a package that only works inside the tldraw monorepo.
- Requiring users to run Yarn workspace commands after installation.
- Hardcoding `C:\Users\...` paths.
- Assuming bash exists on Windows.
- Printing API keys in logs or `doctor` output.
- Replacing previous references when adding a new reference image.
- Treating product images as style, scene, pose, lighting, or composition references.
