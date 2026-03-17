# browser-wrapper-dev

`browser-wrapper-dev` is a Codex skill for building and debugging local wrappers around private web APIs and browser-signed consumer web flows.

It encodes a two-executor workflow:

- Playwright for production execution, browser-signed requests, task submission, polling, and service-owned lifecycle
- `chrome-webmcp` for reverse engineering, DOM inspection, network/console/performance diagnosis, and upload-flow debugging

## Files

- `SKILL.md`: the skill definition
- `agents/openai.yaml`: UI metadata
- `references/wrapper-lessons.md`: lessons generalized from a real wrapper project
- `references/safari-js-injection.md`: Safari-specific fallback tactics for macOS-hosted wrappers

## Install

Copy this folder into your Codex skills directory as `browser-wrapper-dev`, for example:

```bash
cp -R browser-wrapper-dev-skill ~/.codex/skills/browser-wrapper-dev
```

If your environment expects the skill contents rather than the repository root, copy:

- `SKILL.md`
- `agents/`
- `references/`

into a `browser-wrapper-dev/` folder under your skills directory.

## Scope

Use this skill when you are:

- reverse engineering private web endpoints
- building a local API wrapper around a website
- diagnosing browser-signed request flows
- splitting production browser automation from higher-visibility debugging

## License

MIT
