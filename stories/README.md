# Stories

Feature work in this repo requires a story in `stories/` **before** any implementation. See [`.cursor/rules/000-core-safety.mdc`](../.cursor/rules/000-core-safety.mdc).

## Status workflow

| Status | Meaning |
|--------|---------|
| **Draft** | Story proposed; not ready for implementation. |
| **Approved** | User explicitly approved scope and acceptance criteria. Implementation may start. |
| **Done** | All acceptance criteria met; work merged or delivered. |

Do not skip **Approved**. Do not move to **Done** until criteria are satisfied.

## File naming

`NNN-short-slug.md` — e.g. `001-guardrails-cards-page.md`. Reference the story ID (e.g. `STORY-001`) in commits and deliverables.

## Story template

```markdown
# STORY-NNN: Title

## Status
Draft

## Goal
One sentence outcome.

## Acceptance criteria
- [ ] Criterion 1

## Out of scope
- What this story does not include
```

## Agent workflow

1. If no story exists for the requested feature, **stop** and offer to write a **Draft** story only.
2. Wait for the user to set status to **Approved** (or explicit approval in chat).
3. In a **later** session or turn, implement against that story. Link `STORY-NNN` in the work.
4. When complete, update acceptance checkboxes and set **Done** (or ask the user to confirm).

**Forbidden:** Creating `stories/NNN-….md` in the same session or turn as feature code, config, or docs for that feature.
