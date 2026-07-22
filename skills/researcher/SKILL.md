---
name: researcher
description: >-
  Research Instagram and TikTok references through Vidjutsu's tenant-scoped
  scrape and Watch endpoints, using an external discovery API only for keyword
  or hashtag searches that Vidjutsu does not provide.
---

# Researcher

Use `https://api.vidjutsu.ai` with
`Authorization: Bearer <VIDJUTSU_API_KEY>`. Vidjutsu scrape calls share a
500/day limit and return `{ "data": <provider response> }`.

## Define the research target

Collect the niche, platform, success metric, known creators, and desired sample
size. Prefer 10–15 references.

## Discover creators and posts

Use Vidjutsu for supported operations:

| Operation | Endpoint | Body |
|---|---|---|
| Instagram profile | `POST /v1/scrape/instagram/profile` | `{ "handle": "name" }` |
| Instagram reels | `POST /v1/scrape/instagram/user/reels` | `{ "handle": "name", "cursor"?: "..." }` |
| Instagram post/reel | `POST /v1/scrape/instagram/post` | `{ "url": "https://..." }` |
| Instagram comments | `POST /v1/scrape/instagram/post/comments` | `{ "url": "https://...", "cursor"?: "..." }` |
| TikTok profile | `POST /v1/scrape/tiktok/profile` | `{ "handle": "name", "trim"?: true }` |
| TikTok profile videos | `POST /v1/scrape/tiktok/profile/videos` | `{ "handle": "name", "cursor"?: "..." }` |
| TikTok user search | `POST /v1/scrape/tiktok/search/users` | `{ "query": "keyword", "cursor"?: "..." }` |
| TikTok trending | `POST /v1/scrape/tiktok/trending` | `{ "country"?: "us" }` |
| TikTok video | `POST /v1/scrape/tiktok/video` | `{ "url": "https://...", "trim"?: true }` |
| TikTok comments | `POST /v1/scrape/tiktok/video/comments` | `{ "url": "https://...", "cursor"?: "..." }` |
| TikTok transcript | `POST /v1/scrape/tiktok/video/transcript` | `{ "url": "https://..." }` |

Vidjutsu does not currently provide Instagram reel keyword search, TikTok video
keyword search, or TikTok hashtag search. If those discovery operations are
essential, ask the user before using a separately authenticated Scrape Creators
account. Keep its `x-api-key`, responses, and billing explicitly external.

## Stage and analyze a selected video

Scrape returns metadata and raw provider URLs; it does not stage media. For a
selected TikTok or Instagram page URL, call the platform download endpoint once
and use the returned tenant-owned CDN `url`:

```text
POST /v1/videos/download/tiktok   {"url":"<TikTok page URL>"}
POST /v1/videos/download/instagram {"url":"<Instagram page URL>"}
```

Then run:

```bash
vidjutsu watch --mediaUrl "$STAGED_CDN_URL" --prompt 'Return only JSON with hook, format, pacing, transitions, CTA, tags, and durationSeconds.'
```

Watch is synchronous, limited to 50/day, and returns output under `response`.
Never send the social page URL itself to Watch.

## Output

Report ranked hooks, winning formats, pacing and CTA patterns, content gaps,
audience sentiment, and platform differences. Preserve source URLs and evidence
for every conclusion. Vidjutsu uses subscription daily limits.
