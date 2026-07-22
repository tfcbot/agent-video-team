---
name: model-provider
description: >-
  Configure Vidjutsu authentication and the tenant-scoped Kling 3.0 clone
  pipeline. Use before running the director skills or troubleshooting access.
---

# Model Provider

Use Vidjutsu as the model boundary. Do not ask the user for upstream model,
Gateway, or runner credentials.

## Authenticate

The production API base is `https://api.vidjutsu.ai`. Raw API calls use:

```text
Authorization: Bearer <VIDJUTSU_API_KEY>
```

For CLI workflows, store the key once:

```bash
vidjutsu auth --key "$VIDJUTSU_API_KEY"
vidjutsu usage
```

An active `$99/month` subscription is required for metered intelligence and
generation operations. Vidjutsu enforces daily request limits; it does not
charge per-operation fees.

## Execution ownership

- Clone-check, character creation, and starting-image generation run through
  the authenticated tenant's scoped Vercel AI Gateway allocation.
- Clone video is Kling 3.0 Motion Control only. Vidjutsu dispatches it to a
  dedicated runner and receives authenticated lifecycle callbacks.
- The caller stores only the Vidjutsu clone task ID and reads status through
  `GET /v1/clones/video/{id}`. Never call or poll a provider directly.
- No alternate clone-video model or fallback is supported.

## External services

ZapCap captions, Zernio publishing, native platform analytics, and optional
user-supplied music are separate integrations because Vidjutsu currently has
no equivalent endpoints. Keep their credentials and billing separate; never
describe them as tenant Gateway operations.

## Key behaviors

- Default to `model: "kling"`; no other clone model is accepted.
- Check `vidjutsu usage` before a multi-stage workflow.
- Preserve accepted task IDs across retries and resumes.
- Never expose or attempt to obtain the tenant's underlying Gateway key.
