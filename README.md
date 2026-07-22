<h1 align="center">Agent Video Team</h1>

<p align="center">
  <a href="LICENSE"><img src="https://img.shields.io/badge/license-Apache%202.0-blue.svg" alt="License"></a>
</p>

Claude Code skills for short-form video production through VidJutsu, with
clearly labeled external integrations where VidJutsu has no equivalent.

## Install

```bash
npx skills add tfcbot/agent-video-team
```

Authenticate the VidJutsu CLI once:

```bash
vidjutsu auth --key "$VIDJUTSU_API_KEY"
```

Raw API calls use `https://api.vidjutsu.ai` and
`Authorization: Bearer <VIDJUTSU_API_KEY>`. Metered intelligence and generation
operations require an active subscription and use daily request limits, not
per-operation charges. Check remaining capacity with `vidjutsu usage`.

The production plan is a flat `$99/month`. Current daily request limits are:

| Operation group | Requests/day |
|---|---:|
| Watch | 50 |
| Extract | 100 |
| Transcribe | 30 |
| Check | 100 |
| Overlay | 50 |
| Scrape | 500 |
| Agent tasks, including clone admission | 50 |

## Skills

| Skill | What it does |
|---|---|
| [director](skills/director/) | Run the tenant-scoped clone workflow from social import through Kling render and QA. |
| [director-frame-gen](skills/director-frame-gen/) | Extract frame zero, create or reuse a character, and generate a clean starting image. |
| [director-clip-gen](skills/director-clip-gen/) | Submit and monitor Kling-only clone video tasks using Vidjutsu task IDs. |
| [director-qa](skills/director-qa/) | Run anatomy, visual, speech, and VidLang QA gates. |
| [prompt-writer](skills/prompt-writer/) | Write concise Kling Motion Control prompts for an identity-locked starting frame. |
| [critic](skills/critic/) | Analyze video or image quality through Vidjutsu Watch. |
| [editor-post-production](skills/editor-post-production/) | Concat, normalize, overlay, caption, resize, and upload finished videos. |
| [editor-overlay](skills/editor-overlay/) | Burn TikTok-safe text overlays through Vidjutsu. |
| [editor-captions](skills/editor-captions/) | Add animated captions through external ZapCap; Vidjutsu has no caption endpoint. |
| [researcher](skills/researcher/) | Research social references through Vidjutsu scrape methods, retaining external discovery only where needed. |
| [strategist](skills/strategist/) | Create a character, format, and 30-day channel plan. |
| [publisher](skills/publisher/) | Schedule via external Zernio; Vidjutsu post records do not publish content. |
| [publisher-analytics](skills/publisher-analytics/) | Analyze external Zernio and native platform data; Vidjutsu has no analytics endpoint. |
| [model-provider](skills/model-provider/) | Configure Vidjutsu tenant auth and the supported Kling clone model. |

## Contract baseline

These skills target Vidjutsu API contract `2026-07-22`. The repository is
released directly from GitHub `main`; there is no package or tag-based release.
Existing installations update with `npx skills update`.

## License

Apache 2.0
