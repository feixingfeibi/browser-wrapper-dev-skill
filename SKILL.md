---
name: browser-wrapper-dev
description: Build, modify, debug, or review a local wrapper around a website's private web endpoints, browser-signed requests, upload flows, or login-state-dependent APIs. Use when the task involves reverse engineering a consumer web app into a maintainable local service or script, especially when work should be split between a production executor such as Playwright and a debugging sidecar such as chrome-webmcp. Triggers include browser-signed fetch, private web API wrapper, local API around a website, reverse engineering upload/signature flows, DOM-driven task submission, or turning captured browser behavior into code.
---

# Browser Wrapper Development

## Start Here

- Confirm whether the task is:
  - production execution work
  - reverse engineering / diagnosis work
  - or converting discoveries into a wrapper API
- Read the target repo's `AGENTS.md` first if one exists.
- Prefer the smallest relevant files first: runtime entrypoint, browser executor, route layer, auth/cookie code, and debug helpers.

## Runtime Split

- Use Playwright or another owned in-process browser runtime for:
  - production execution
  - browser-signed requests
  - task submission and polling
  - service-owned lifecycle, retries, and shutdown
  - repeatable uploads and login-state-dependent flows
- Use `chrome-webmcp` for:
  - reverse engineering
  - network / console / performance capture
  - DOM and hidden upload control inspection
  - checking what the page actually does before changing code
  - one-off interactive diagnosis
- Do not make `chrome-webmcp` the default runtime for a local API server unless the whole system is intentionally built around an external browser controller.
- Do not bounce between the two executors inside one production flow. Observe with `chrome-webmcp`, then encode the result back into Playwright or the repo's runtime.

## Default Workflow

1. Identify the real production path.
2. Confirm whether the site needs only cookies or also browser-side signing.
3. Use `chrome-webmcp` to inspect the live page when behavior is unclear.
4. Capture the minimum useful facts:
   - exact request sequence
   - required query params / headers
   - frontend signing calls
   - upload widget behavior
   - DOM state needed before submission
5. Implement the stable path in Playwright or the repo's owned runtime.
6. Keep debug-only helpers behind explicit debug flags.
7. Validate auth, submit, and polling separately before widening scope.

## Read By Task

- For a concrete repo under development, start with:
  - service entrypoint
  - browser runtime / executor
  - route or API layer
  - auth / cookie / session helpers
  - task builders or payload builders
- If the repo already has a site-specific wrapper example, read the matching reference note in `references/`.
- For lessons from a real consumer-web wrapper project, read `references/wrapper-lessons.md`.
- For Safari-only sites or Mac-hosted wrappers driven by AppleScript JS injection, read `references/safari-js-injection.md`.

## Guardrails

- Treat browser ownership as an architectural choice, not an implementation detail.
- Prefer one clearly owned runtime for production code.
- Keep raw request proxies and capture helpers out of the default happy path.
- Return real upstream failures instead of masking them as success.
- Gate debug routes explicitly and document that they may be disabled by default.
- Preserve exact observed behavior before "cleaning up" a flow that is fragile.

## Platform Fallbacks

- If Chrome/CDP-style control is unavailable or the target flow is already anchored to Safari on macOS, use a Safari fallback instead of forcing the default stack.
- In that case, treat AppleScript `do JavaScript` as the page-evaluation primitive, and keep the same separation of concerns:
  - Safari JS injection for execution
  - higher-visibility inspection tools only for diagnosis
- Prefer Safari fallback only when it is the simplest reliable path for that site or environment.
- Do not generalize a Safari-specific tactic into the default architecture for all wrappers.

## Common Pitfalls

- Assuming cookies are enough when the site also requires browser-side signing.
- Replacing a service-owned runtime with an external browser tool just because the tool is nicer for inspection.
- Mixing debugging and production execution paths until session state becomes ambiguous.
- Depending on request sniffing or DOM capture in a production path longer than necessary.
- Building generic abstractions before the real request sequence is understood.
- Treating upload flows as normal HTTP when the site relies on hidden inputs, frontend modules, or risk-control checks.

## Preferred Outputs

- A stable wrapper route or script for the production path
- A narrow debug helper for the unresolved part
- A short note describing:
  - what was observed with `chrome-webmcp`
  - what was encoded into Playwright
  - what remains intentionally debug-only

## References

- `references/wrapper-lessons.md`
- `references/safari-js-injection.md`
