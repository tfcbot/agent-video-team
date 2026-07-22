---
name: editor-post-production
description: >-
  Assemble and finalize passed Vidjutsu clone videos with concat, loudness
  normalization, Vidjutsu overlay, optional external captions, resizing, and upload.
---

# Editor — Post Production

Run only the applicable steps. Preserve a pre-edit copy and reuse every
successful Vidjutsu result.

## 1. Concat

Join passed clips with hard cuts:

```bash
ffmpeg -f concat -safe 0 -i concat.txt -c copy concat.mp4
```

If the final video should be silent, strip audio explicitly. Otherwise preserve
the Kling source soundtrack.

## 2. Normalize speech

For spoken content, apply two-pass FFmpeg loudnorm at -16 LUFS. Verify the
result through Vidjutsu:

```bash
vidjutsu transcribe --mediaUrl "$PUBLIC_VIDEO_URL"
```

Vidjutsu does not replace audio tracks or voices. Any separately authorized audio
replacement is an external edit and must be labeled as such; it is not part of
the tenant-scoped generation flow.

## 3. Overlay

```bash
vidjutsu overlay --videoUrl "$VIDEO_URL" --text "$OVERLAY_TEXT" --position bottom
```

The synchronous response contains `{id,resultUrl}`. Use `resultUrl` downstream.
Overlay is limited to 50 requests/day and is included in the subscription.

## 4. Captions

Vidjutsu does not render animated captions. If the user requests them, run
the external `/editor-captions` ZapCap workflow after overlay and disclose its
separate credential and billing.

## 5. Optional music

Vidjutsu does not generate music. Use only a user-supplied or
separately authorized external music file, then mix it locally. Do not invent a
Vidjutsu route or embed upstream-task polling in this skill.

## 6. Edit and resize

- Trim dead air at the start and end.
- Shorten silence gaps only when speech remains natural.
- Mute timestamped artifacts.
- Scale and pad to 1080x1920 only when the destination requires it.

```bash
ffmpeg -i output.mp4 -vf "scale=1080:1920:force_original_aspect_ratio=decrease,pad=1080:1920:(ow-iw)/2:(oh-ih)/2:black" -c:a copy final.mp4
```

## 7. Upload and verify

```bash
vidjutsu upload final.mp4
vidjutsu watch --mediaUrl "$FINAL_CDN_URL" --prompt 'Return only JSON with pass:boolean and issues:string[]. Verify one continuous identity, readable requested text, clean framing, intact audio, and no severe artifacts.'
```

The upload response contains `{assetId,url,key,size}`. Watch output is nested in
`response`. Report the final CDN URL, size, duration, Kling model, QA result,
and applied post-production steps.
