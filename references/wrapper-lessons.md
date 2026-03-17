# Wrapper Lessons

Use this note when a task resembles a real-world site wrapper that reuses browser login state and frontend request signing.

## What Mattered

- The hard part was not opening a page. The hard part was reproducing browser-signed execution reliably inside a local service.
- The stable route was an owned Playwright runtime that could:
  - hold a page/context
  - execute frontend signing code in-page
  - submit requests from browser `fetch`
  - poll tasks and recover from page death
- `chrome-webmcp` was still valuable, but mainly for diagnosis and reverse engineering.

## Reusable Rules

- Prefer a service-owned runtime for production browser execution.
- Use `chrome-webmcp` to discover behavior, not to replace the runtime by default.
- When the site needs frontend signing, model the runtime around page evaluation and browser-side `fetch`, not raw HTTP replay.
- Separate:
  - production routes
  - raw debug routes
  - reverse-engineering helpers
- Disable debug-only routes by default.

## Specific Pitfalls

- Cookie reuse alone may be insufficient if the site also computes request signatures in frontend bundles.
- A generic request proxy is useful for discovery, but it should not become the main product surface unless the wrapper is intentionally low-level.
- A production upload flow should not depend on sniffing browser traffic forever. If request capture is still on the happy path, target that area for later hardening.
- If login state gets more complex than cookie copy can handle, consider attaching to a real browser via CDP before inventing more cookie glue.
- Do not over-generalize too early. First prove auth, then submit, then status, then uploads, then high-level builders.

## Practical Split

- Playwright:
  - auth-sensitive execution
  - browser-signed requests
  - submit / poll
  - repeatable uploads
  - runtime retries and shutdown
- `chrome-webmcp`:
  - inspect DOM state
  - capture network sequence
  - verify console errors
  - inspect hidden upload controls
  - confirm whether a frontend change broke the flow

## Apply This Elsewhere

If a new target site has:

- private `/api/...` routes
- browser-session dependency
- frontend-generated signatures
- complex upload widgets
- brittle SPA state before submit

then reuse the same strategy:

1. Diagnose with `chrome-webmcp`.
2. Encode the stable path in Playwright.
3. Keep debug helpers separate from production routes.
