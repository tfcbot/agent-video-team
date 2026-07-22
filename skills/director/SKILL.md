---
name: director
description: >-
  Orchestrate Vidjutsu's tenant-scoped short-form clone workflow: stage a social
  source, check cloneability, create or reuse a character, compose the first
  frame, render with Kling 3.0, run QA, and finish post-production.
---

# Director

Run the production workflow in dependency order. Reuse every completed artifact
and never repeat a paid generation merely because a later stage failed.

Use `https://api.vidjutsu.ai` with
`Authorization: Bearer <VIDJUTSU_API_KEY>`. The generation and intelligence
stages require an active subscription and share fixed daily limits.

## Inputs

Collect the TikTok or Instagram source URL, a reusable character ID or character
description, optional motion prompt, optional overlay text, and whether external
ZapCap captions are wanted.

## Pipeline

1. **Stage the source once.** Use the platform-specific Vidjutsu download
   method and retain its CDN `url` and `assetId`.
2. **Check cloneability.** Call `POST /v1/clones/check` with the staged
   `videoUrl`. Treat returned `{verdict,score,evidence,model}` as authoritative;
   stop on `weak` unless the user explicitly overrides it.
3. **Extract frame zero.** Call `POST /v1/extract` with
   `{ "mediaUrl": sourceUrl, "frames": [0] }`; retain `frames[0].url`.
4. **Resolve identity.** Reuse a tenant-owned `char_...` ID, or call
   `POST /v1/characters` once with `{prompt, referenceImageUrl?}`.
5. **Create the starting image.** Call `POST /v1/clones/starting-image` with
   exactly `{firstFrame, characterId, prompt}`.
6. **Submit the clone.** Call `POST /v1/clones/video` with
   `{startingImageUrl, sourceVideoUrl, model:"kling", prompt?}`. Source clips
   must be 3–15 seconds.
7. **Persist and poll the Vidjutsu task ID.** Read only
   `GET /v1/clones/video/{id}`. While `processing`, resume later; on
   `completed`, retain `videoUrl`; on `failed`, report `error`.
8. **Run QA.** Use `/director-qa` on the completed Vidjutsu URL.
9. **Post-process.** Use `/editor-post-production` only after QA passes.

## Non-negotiable rules

- Kling 3.0 Motion Control is the sole clone-video model.
- Never use an alternate model, upstream task ID, or upstream status endpoint
  in this workflow.
- Do not resubmit while a Vidjutsu task remains `processing`.
- Vidjutsu Watch and generation operations require an active subscription and
  consume daily request capacity rather than per-operation charges.
- External captions or publishing remain separate and must be labeled as such.
