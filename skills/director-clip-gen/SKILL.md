---
name: director-clip-gen
description: >-
  Submit and monitor a Kling 3.0 Motion Control clone through Vidjutsu using a
  staged source video, a generated starting image, and a Vidjutsu-owned task ID.
---

# Director — Clip Gen

Vidjutsu supports one clone-video model: Kling 3.0 Motion Control.

## Submit

```http
POST https://api.vidjutsu.ai/v1/clones/video
Authorization: Bearer <VIDJUTSU_API_KEY>
Idempotency-Key: <stable workflow key>
Content-Type: application/json

{
  "startingImageUrl": "<generated starting image URL>",
  "sourceVideoUrl": "<staged source video URL>",
  "model": "kling",
  "prompt": "<optional concise motion-control guidance>"
}
```

The source must be public HTTPS and 3–15 seconds. Acceptance is asynchronous:

```json
{"id":"<Vidjutsu clone task ID>","status":"processing"}
```

Persist `id` immediately. Reusing the same idempotency key prevents duplicate
generation when the original request outcome is replayed.

## Read status

```http
GET https://api.vidjutsu.ai/v1/clones/video/{id}
Authorization: Bearer <VIDJUTSU_API_KEY>
```

- `processing`: checkpoint the task ID and check again later.
- `completed`: use the returned `videoUrl`.
- `failed`: stop and report the returned `error`.

Status reads use Vidjutsu-owned state updated by authenticated runner callbacks.
They do not poll Kling and do not expose an upstream task ID.

## Rules

- Never select or fall back to another model or Kling version.
- Never call the dedicated runner or an upstream provider directly.
- Never resubmit merely because a task is still `processing`.
- Preserve the returned video without resizing or transcoding until
  post-production.
- Clone submissions share the 50/day clone admission group; status reads are
  unmetered and have no separate per-operation charge.
