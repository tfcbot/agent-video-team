---
name: prompt-writer
description: >-
  Write concise Kling 3.0 Motion Control prompts for Vidjutsu clone videos.
  Use when a staged source and identity-locked starting image define the motion,
  framing, and character, and the caller needs an optional motion prompt.
---

# Prompt Writer

Write one concise optional prompt for Kling Motion Control. The source video
already defines motion, timing, camera, and sound; the starting image already
defines identity and initial composition.

## Inputs

Collect the intended tone, any motion behavior that must be preserved, any
scene-specific exclusions, and the requested overlay or caption text. Do not
place overlay text in the generation prompt; add it in post-production.

## Prompt structure

Use 30–60 words:

```text
Preserve the source performance, timing, camera motion, and original sound.
Maintain the starting-image identity and wardrobe throughout. [Optional
scene-specific continuity or expression guidance.] Avoid identity drift,
warping, extra limbs, broken hands, and added on-screen text.
```

## Rules

- Describe only constraints that improve motion-control fidelity.
- Do not duplicate the character description; the starting image carries it.
- Do not request a different aspect ratio, resolution, duration, sound mode,
  provider, or model. Those are not prompt parameters.
- Do not provide alternate-model, upstream-provider, extra request-field, or
  upstream-task instructions.
- Keep the source soundtrack unless the user later performs a clearly labeled
  external edit. Vidjutsu does not replace audio tracks.

## Output

Return the optional `prompt` string plus any post-production overlay/caption
text as separate fields. If the default motion-control behavior is sufficient,
recommend omitting `prompt` entirely.
