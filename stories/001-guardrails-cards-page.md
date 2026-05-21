# STORY-001: Guardrails cards page

## Status

Done (delivered before story-gate rules; see [README](README.md))

## Goal

Single static page in `app/` showing the four repo guardrails as Bootstrap cards.

## Acceptance criteria

- [x] `app/index.html` opens locally and renders four cards
- [x] Each card maps to one `.cursor/rules/*.mdc` file
- [x] Bootstrap 5 only (CDN); no React/Vue/etc.
- [x] Vanilla JS only if needed (e.g. data array → DOM); no other frameworks
- [x] Page title and short intro explain purpose
- [x] Each card: title, 1-line summary, bullet list of key rules, link to source rule file

## Out of scope

- Build tooling, npm, backend, CI changes
- Editing rule content (display only)
