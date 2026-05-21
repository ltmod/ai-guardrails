# STORY-002: Web URL permissions page

## Status

Draft

## Goal

Static page in `app/` listing pre-approved WebFetch domains from `.claude/settings.json`.

## Acceptance criteria

- [ ] `app/web-permissions.html` opens locally and renders a Bootstrap table
- [ ] One row per `WebFetch(domain:…)` entry in `permissions.allow`
- [ ] Columns: **Domain**, **Permission** (Allow), **Category** (grouped label for readability)
- [ ] Footer row or callout: unlisted domains → **Ask** (prompt before fetch)
- [ ] Link to source: `.claude/settings.json` and [docs/claude-permissions.md](../docs/claude-permissions.md)
- [ ] Cross-navigation between `index.html` and `web-permissions.html`
- [ ] Bootstrap 5 CDN only; static HTML (no build, no fetch-at-runtime)
- [ ] `STORY-002` comment in HTML footer

## Domain rows (from current settings)

| Domain | Permission | Category |
|--------|------------|----------|
| docs.anthropic.com | Allow | Documentation |
| docs.claude.com | Allow | Documentation |
| github.com | Allow | Code hosting |
| raw.githubusercontent.com | Allow | Code hosting |
| gist.github.com | Allow | Code hosting |
| developer.mozilla.org | Allow | Documentation |
| stackoverflow.com | Allow | Documentation |
| nodejs.org | Allow | Language docs |
| python.org | Allow | Language docs |
| docs.python.org | Allow | Language docs |
| pypi.org | Allow | Package registry |
| registry.npmjs.org | Allow | Package registry |
| www.npmjs.com | Allow | Package registry |

## Out of scope

- Build tooling, npm, backend, CI changes
- Editing `.claude/settings.json`
- Dynamic JSON parsing at runtime
