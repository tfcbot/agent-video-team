---
name: critic
description: Analyze video and image content through Vidjutsu Watch for quality checks, verification, and deep analysis before publishing.
---

# Critic

Use Vidjutsu Watch for quality analysis. The production API base is
`https://api.vidjutsu.ai`; raw calls require bearer auth. Watch is synchronous,
limited to 50 requests/day, and included in the subscription.

## Quality check

```bash
vidjutsu watch --mediaUrl "$MEDIA_URL" --prompt 'Return only JSON: {"score": number, "issues": [{"severity": "minor"|"major"|"critical", "description": string}]}. Score face consistency, artifacts, motion, and audio sync.'
```

The API response envelope is:

```json
{"response":{"score":8,"issues":[]}}
```

Read model output from `response`; do not expect `score` at the top level.

## Verification

```bash
vidjutsu watch --mediaUrl "$MEDIA_URL" --prompt 'Return only JSON describing whether this video matches: <description>. Include matches:boolean and discrepancies:string[].'
```

## Deep analysis

```bash
vidjutsu watch --mediaUrl "$MEDIA_URL" --prompt 'Return only JSON with hook, format, pacing, transitions, CTA, and tags.'
```

## Rules

- Supply a fetchable media URL, not an Instagram or TikTok page URL. Stage
  social sources first with the Vidjutsu download endpoint.
- Parse and validate `response` against the schema requested in the prompt.
- If a video fails twice, change the generation prompt before retrying.
- Run quality QA on every final video before publishing.
