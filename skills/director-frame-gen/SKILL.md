---
name: director-frame-gen
description: >-
  Prepare a Vidjutsu clone starting image by extracting source frame zero,
  creating or reusing a tenant-owned character, and composing the frame through
  the tenant-scoped Gateway.
---

# Director — Frame Gen

Create the identity-locked starting image required by Kling Motion Control.

## 1. Extract frame zero

Use the raw API so frame zero is sent as the required numeric array. The call is
synchronous:

```http
POST https://api.vidjutsu.ai/v1/extract
Authorization: Bearer <VIDJUTSU_API_KEY>
Content-Type: application/json

{"mediaUrl":"<staged source URL>","frames":[0]}
```

Use `frames[0].url` as `firstFrame`. Extract is limited to 100 requests/day.

## 2. Resolve a reusable character

Prefer an existing tenant-owned character ID. Otherwise create one once:

```http
POST https://api.vidjutsu.ai/v1/characters
Authorization: Bearer <VIDJUTSU_API_KEY>
Content-Type: application/json

{"prompt":"<identity description>","referenceImageUrl":"<optional HTTPS image>"}
```

Store the synchronous response `{id,imageUrl,model}` and reuse `id`.

## 3. Create the starting image

```http
POST https://api.vidjutsu.ai/v1/clones/starting-image
Authorization: Bearer <VIDJUTSU_API_KEY>
Content-Type: application/json

{
  "firstFrame": "<frames[0].url>",
  "characterId": "<char_...>",
  "prompt": "Replace only the performer identity while preserving pose, framing, lighting, clothing silhouette, background, and composition."
}
```

This call is synchronous and returns `{imageUrl,model}`. Vidjutsu sends the two
images through the authenticated tenant's scoped Gateway allocation and
enforces removal of captions, watermarks, and platform UI.

## Rules

- `firstFrame`, `characterId`, and `prompt` are all required.
- Do not send legacy identity-image or source-video fields.
- Do not call an image provider directly.
- Reuse completed extraction, character, and starting-image results.
- These stages share the 50/day clone admission group and have no separate
  per-operation charge.
