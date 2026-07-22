---
name: editor-overlay
description: >-
  Burn TikTok-safe text overlays onto videos through Vidjutsu with configurable
  font size, stroke thickness, and top, center, or bottom positioning.
---

# Editor — Overlay

Use the synchronous Vidjutsu overlay endpoint. Production base:
`https://api.vidjutsu.ai`; raw requests require
`Authorization: Bearer <VIDJUTSU_API_KEY>`.

```bash
vidjutsu overlay --videoUrl "$VIDEO_URL" --text 'Follow for more tips' --position bottom
```

For numeric customization, call the API directly so the values remain JSON
numbers:

```bash
curl -sS -X POST https://api.vidjutsu.ai/v1/overlay \
  -H "Authorization: Bearer $VIDJUTSU_API_KEY" \
  -H 'Content-Type: application/json' \
  -d "{\"videoUrl\":\"$VIDEO_URL\",\"text\":\"Follow for more tips\",\"position\":\"bottom\",\"fontSize\":48,\"strokeThickness\":2}"
```

Request fields:

| Field | Required | Contract |
|---|---:|---|
| `videoUrl` | yes | Public HTTPS video URL |
| `text` | yes | Overlay text; `\n` creates line breaks |
| `position` | no | `top`, `center`, or `bottom`; defaults to `bottom` |
| `fontSize` | no | Integer pixels; defaults to 4% of video height |
| `strokeThickness` | no | Integer 0–10; defaults to 2 |

The response is `{ "id": "ovl_...", "resultUrl": "https://..." }`.
Use `resultUrl` for later captioning or QA.

Overlay is included in the subscription and limited to 50 calls/day. Vidjutsu
does not render animated captions; use the explicitly external
`/editor-captions` workflow only when requested.
