# Safari JS Injection

Use this note when a site wrapper must run through Safari on macOS, especially when standard Chrome DevTools Protocol control is unavailable or not worth introducing.

This pattern was reinforced by the public `ythx-101/grok-bridge` project, which uses Safari automation plus JavaScript injection instead of a Chrome-style browser driver.

## When This Fallback Makes Sense

- The target site is already open and authenticated in Safari.
- The environment is macOS-only.
- CDP-style browser control is unavailable, awkward, or unnecessary.
- The wrapper can tolerate site-specific DOM selectors and page polling.
- The goal is a narrow site bridge, not a cross-browser runtime abstraction.

## Core Tactics

- Use AppleScript `do JavaScript` as the page evaluation primitive.
- Inject JavaScript into the active Safari tab to:
  - locate the real input element
  - focus the element
  - write text in a way the frontend accepts
  - trigger submit by DOM click or a final keyboard fallback
  - poll page text or DOM markers until the response stabilizes
- Keep selectors plural and defensive because consumer sites change UI often.

## Why This Can Beat a Heavier Stack

- It keeps the runtime small for a single-site macOS bridge.
- It avoids adding a separate browser automation dependency when Safari is already the source of truth.
- It can work around controlled-input behavior if normal value setters are ignored.

## Pitfalls

- This is site-specific and UI-fragile.
- DOM text extraction is weaker than a structured network-backed wrapper.
- AppleScript/Safari control is not a portable default for general wrapper development.
- Public examples may expose operational details in docs; scrub IPs, local hostnames, and environment-specific instructions before reusing the pattern.

## Apply It Correctly

1. Prove the site can be driven reliably from Safari.
2. Keep the bridge narrow and explicit about its platform scope.
3. Reuse the main `browser-wrapper-dev` guardrails:
   - one owned execution path
   - separate diagnosis from production behavior
   - do not mix fallback tactics into unrelated wrappers by default

## Good Fit

- A personal or team bridge around one authenticated web app on macOS
- Fast prototyping for a Safari-only workflow
- Situations where browser evaluation plus DOM extraction is enough

## Bad Fit

- A reusable multi-site wrapper framework
- Long-lived production systems that need robust observability and structured upstream responses
- Cases where network/API capture has already shown that a browser-signed Playwright runtime would be cleaner
