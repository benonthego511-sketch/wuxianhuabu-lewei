# Data contracts

## ReferenceRole

```ts
export type ReferenceRole =
  | 'product'
  | 'scene'
  | 'pose'
  | 'composition'
  | 'lighting'
  | 'style'
  | 'material'
  | 'negative'
```

## GenerateReference

```ts
export type GenerateReference = {
  id: string
  sourceNodeId: string
  imageUrl: string
  role: ReferenceRole
  weight: number
  instruction: string
  enabled: boolean
  order: number
}
```

Rules:

- `id` should uniquely identify the reference edge/input.
- `sourceNodeId` points to the source image-like node.
- `imageUrl` must preserve the original independent image.
- `role` must be explicit before entering generation.
- `weight` should default to `1` unless the UI or edge specifies another value.
- `instruction` should default from role-specific guidance but remain user-editable.
- `enabled=false` keeps the connection visible but excludes it from generation.
- `order` controls priority without replacing references.

## GenerateNodeData

```ts
export type GenerateNodeData = {
  prompt: string
  negativePrompt?: string
  references: GenerateReference[]
  outputSettings: {
    model: string
    aspectRatio: string
    size: string
    quality: string
  }
}
```

## CanvasEdgeData

```ts
export type CanvasEdgeData = {
  sourceNodeId: string
  targetNodeId: string
  sourceHandle?: string
  targetHandle?: string
  role?: ReferenceRole
  weight?: number
  instruction?: string
  enabled?: boolean
}
```

Rules:

- Image-like nodes may connect to GenerateNode.
- GenerateNode must accept many image inputs.
- Do not disable the input port after the first connection.
- Connecting a new image appends a reference. It must not overwrite existing references.
- Updating an edge role updates only the matching reference.
- Deleting an edge deletes only the matching reference.

## GeneratePayload

```ts
export type GeneratePayload = {
  prompt: string
  negativePrompt?: string
  references: GenerateReference[]
  outputSettings: {
    model: string
    aspectRatio: string
    size: string
    quality: string
  }
}
```

Only `references.filter((ref) => ref.enabled && ref.role !== 'negative')` should be sent as positive image references to image generation models.

Negative references should contribute to negative instructions, not positive image conditioning, unless a provider explicitly supports negative image references.

## Product isolation rules

A product reference may lock:

- structure
- proportions
- material
- color
- logo
- texture
- fine product details

A product reference must not drive:

- background
- scene
- lighting
- composition
- human pose
- photography style

Default product instruction:

```text
Keep the product structure, proportions, material, color, logo, texture, and details consistent. Do not change the style, do not add extra decoration, and do not copy the product image background.
```

## Role prompt guidance

- `product`: use only product appearance and structure.
- `scene`: use background, environment, atmosphere, and setting only.
- `pose`: use body or object pose only.
- `composition`: use framing and layout only.
- `lighting`: use lighting direction, contrast, and mood only.
- `style`: use photography or visual style only.
- `material`: use material texture and surface feel only.
- `negative`: explicitly avoid imitating this reference.

## Anti-regression checks

- `references` must be an array, never a single object.
- Adding image 2 or image 3 must not replace image 1.
- UI must not render only `references[0]`.
- Backend must not accept only `imageUrl` when `references` is provided.
- Model layer must not keep only the first or last image.
- Multiple images must not be composited into one image before generation.
