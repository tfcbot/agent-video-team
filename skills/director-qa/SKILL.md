---
name: director-qa
description: >-
  Run Vidjutsu QA gates on completed clone videos: anatomy frames, structured
  Watch criticism, transcript verification, and explicitly enabled VidLang rules.
---

# Director — QA

Run each gate in order. If a gate fails, revise the motion prompt and submit a
new Vidjutsu clone only after the prior task is terminal. Allow at most five
total generation attempts.

## Gate 1 — Anatomy

Extract three frames from the completed clone:

```bash
vidjutsu extract --mediaUrl "$CLIP_URL" --frames auto
```

For each returned frame URL, request an explicit response schema:

```bash
vidjutsu watch --mediaUrl "$FRAME_URL" --prompt 'Return only JSON: {"score":number,"issues":[string]}. Trace every visible limb to the torso and check joint position, body-part count, and spatial continuity. Score 8+ only when anatomy is plausible.'
```

Read `response.score`. Reject if any frame scores below 8. Extract is limited to
100/day; Watch is limited to 50/day.

## Gate 2 — Visual critic

```bash
vidjutsu watch --mediaUrl "$CLIP_URL" --prompt 'Return only JSON: {"score":number,"issues":[{"severity":"minor"|"major"|"critical","category":string,"description":string}]}. Check identity continuity, anatomy, artifacts, motion, framing, and audio sync.'
```

Read the result from the API's `response` envelope. Reject for a score below 8
or any major/critical issue. Adapt the prompt to the content style.

## Gate 3 — Speech

Skip when there is no spoken dialogue.

```bash
vidjutsu transcribe --mediaUrl "$CLIP_URL"
```

Compare the returned `transcript` with intended dialogue. Reject missing,
incorrect, or repeated phrases. Number-word normalization is non-blocking.
Vidjutsu transcribe is limited to 30/day. This gate verifies the original Kling
soundtrack; Vidjutsu does not replace voices.

## Gate 4 — VidLang rules

Every `POST /v1/check` request requires both `spec` and `rules`, and at least
one rule must be enabled. Rules are off by default; an omitted, empty, or
all-disabled rules object is invalid.

```bash
vidjutsu check run --spec ./spec.json --rules '{"VL013":true,"VL011":{"severity":"warning"}}'
```

Use `true` to enable a rule, `false` or `{ "enabled": false }` to disable it,
and `{ "severity": "warning" }` to enable with an override.

Custom plain-text rules are stored separately:

```bash
vidjutsu check rules list
vidjutsu check rules set 'Dialogue contains the AI reveal,Requested CTA is present'
```

`GET` and `PUT /v1/check/rules` are unmetered. Evaluate those strings in the
agent layer; Vidjutsu stores but does not execute them. Built-in checks are
limited to 100/day.

## Finish

For each passing clip, report its URL, Watch scores, enabled VidLang rules, and
attempt count. To chain another scene, extract the final frame with:

```bash
vidjutsu extract --mediaUrl "$CLIP_URL" --frames last
```

Do not continue to post-production if any required gate fails.
