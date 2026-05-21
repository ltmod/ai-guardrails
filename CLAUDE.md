# Project Rules

This project's operating rules are maintained in `.cursor/rules/` so they apply to both Cursor and Claude Code from a single source. Follow these as binding rules for every task in this repo.

- [Core safety](.cursor/rules/000-core-safety.mdc) — scope, stories before code, confirmation, risky operations
- [Stories](stories/README.md) — draft → approved → done; never ship story and feature in one turn
- [Git workflow](.cursor/rules/100-git-workflow.mdc) — no destructive or force operations
- [Database safety](.cursor/rules/200-database-safety.mdc) — migrations only, no production SQL
- [Security](.cursor/rules/300-security.mdc) — secrets, CI/CD, deployment

If a rule in those files conflicts with default Claude Code behavior, the rule file wins. When the user edits any `.mdc` file, treat the updated content as authoritative on the next turn.
