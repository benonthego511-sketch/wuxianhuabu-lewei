# API contract

## GET /api/health

Response:

```json
{
  "ok": true,
  "version": "1.0.0",
  "platform": "windows",
  "storageReady": true,
  "adapterReady": true
}
```

`platform` should be derived from Node `process.platform` and normalized to `windows`, `macos`, or another clear value.

## POST /api/generate

Request:

```json
{
  "prompt": "Generate a realistic e-commerce image",
  "negativePrompt": "Do not change the product style",
  "references": [
    {
      "id": "ref-1",
      "sourceNodeId": "node-product",
      "imageUrl": "upload://shoe.png",
      "role": "product",
      "weight": 1,
      "instruction": "Keep the product appearance exactly. Do not copy the white background.",
      "enabled": true,
      "order": 0
    }
  ],
  "outputSettings": {
    "model": "default",
    "aspectRatio": "1:1",
    "size": "1024x1024",
    "quality": "standard"
  }
}
```

Response:

```json
{
  "ok": true,
  "imageUrl": "http://localhost:3000/outputs/generated.png",
  "payload": {}
}
```

Rules:

- Reject enabled image references without role.
- Keep references as independent array entries.
- Do not composite references into one image.
- Include final resolved payload in debug mode or response metadata when safe.
- Do not expose API keys.

## POST /api/assets/upload

Accept multipart form uploads.

Response:

```json
{
  "ok": true,
  "assets": [
    {
      "id": "asset-1",
      "url": "http://localhost:3000/uploads/image.png",
      "filename": "image.png",
      "mimeType": "image/png"
    }
  ]
}
```

## POST /api/canvas/import

Request:

```json
{
  "canvas": {}
}
```

Response:

```json
{
  "ok": true,
  "canvasId": "canvas-1"
}
```

## POST /api/canvas/export

Request:

```json
{
  "canvasId": "canvas-1"
}
```

Response:

```json
{
  "ok": true,
  "canvas": {}
}
```

## GET /api/history

Response:

```json
{
  "ok": true,
  "items": []
}
```

## Error shape

Use a consistent error response:

```json
{
  "ok": false,
  "error": "Human readable message",
  "code": "MISSING_REFERENCE_ROLE"
}
```

Recommended error codes:

- `MISSING_REFERENCE_ROLE`
- `INVALID_REFERENCE_ROLE`
- `NO_ENABLED_REFERENCES`
- `ADAPTER_NOT_CONFIGURED`
- `GENERATION_FAILED`
- `UPLOAD_FAILED`
- `CANVAS_IMPORT_FAILED`
- `CANVAS_EXPORT_FAILED`
- `STORAGE_NOT_READY`
