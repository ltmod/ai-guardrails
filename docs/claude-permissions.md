# Claude Code Permissions

Claude Code gates every tool call (file read, file edit, shell command, etc.) through a permission system. The settings in `.claude/settings.json` decide which calls run silently, which prompt you for confirmation, and which are blocked outright.

This guide explains the three permission modes, the principle behind how we set them, and why this matters specifically when you are working with an AI agent.

---

## The three modes: allow, ask, deny

Every tool call is matched against three lists in `.claude/settings.json` → `permissions`.

### `allow` — run without asking

The tool call proceeds immediately. No prompt, no confirmation.

Use `allow` for operations that are read-only and have a small blast radius — things you would never want to be interrupted to approve.

```json
"allow": [
  "Read",
  "Glob",
  "Grep",
  "Bash(git status)",
  "Bash(git diff *)"
]
```

In this repo we allow file reads, search tools, and read-only git inspection (`status`, `diff`, `log`, `show`, `branch`). The agent can navigate and understand the codebase freely.

We also pre-approve a small set of trusted **WebFetch** domains — Anthropic docs, GitHub, MDN, Stack Overflow, npm/PyPI registries, and core language docs. Fetches to any other URL fall through to the default prompt, so an unfamiliar link cannot be opened silently.

### `ask` — prompt before running

Claude pauses and shows you the exact tool call. You approve or reject it before anything happens.

Use `ask` for operations that change state — file edits, file writes, and shell commands you have not specifically pre-approved.

```json
"ask": [
  "Edit",
  "Write",
  "NotebookEdit",
  "Bash"
]
```

This is the safety net: by default, anything that writes to disk or runs in your shell stops for review. You see the diff (for edits) or the exact command (for Bash) before it runs.

### `deny` — refuse entirely

The tool call is blocked. No prompt — you cannot even approve it from inside the session.

Use `deny` for operations that should never happen from an AI agent, regardless of how confident the agent (or you) feels in the moment. The point is to take a class of mistakes off the table.

```json
"deny": [
  "Bash(git push --force*)",
  "Bash(terraform apply*)",
  "Bash(psql *production*)",
  "Read(./.env)"
]
```

In this repo we deny:

- **Destructive git** — `push --force`, `reset --hard`, `clean -f`, `branch -D`, `checkout --`, `restore --`, interactive rebase.
- **Production deploys** — `terraform apply`/`destroy`, `npm/yarn/pnpm publish`, `docker push`, `gh workflow run`, `gh release create`, `kubectl --context prod`.
- **Production database access** — any `psql`, `mysql`, or `mongosh` command containing `prod` or `production`.
- **Secret files** — `.env`, `.env.*`, `*.pem`, `*.key`, SSH private keys, anything under `secrets/`, `credentials*`.

If the agent ever tries one of these, it gets a hard "no" — and you find out something interesting happened.

### Precedence

When the same call matches more than one list, **`deny` wins, then `ask`, then `allow`**. So an `allow` rule cannot accidentally re-enable something a `deny` rule blocks.

---

## Least privilege

The configuration follows the **principle of least privilege**: grant the smallest set of permissions that lets the agent do useful work, and require a human in the loop for anything beyond that.

Concretely:

- **Reading is cheap.** Reading a file or running `git diff` doesn't change anything. Allow it broadly so the agent can investigate without 50 confirmation prompts.
- **Writing requires consent.** Edits, file writes, and shell commands all stop for approval by default. You see exactly what will happen before it happens.
- **Some things stay off the table.** Force-pushing, dropping prod data, publishing to npm, reading `.env` — these are denied even if you would approve them in the moment. If you genuinely need to do one of these, do it yourself outside the session.

A useful test for any new rule: *"If I imagine the agent doing this when I'm not paying close attention, am I OK with that?"* If yes → `allow`. If "only with my eyes on it" → `ask`. If "never from the agent" → `deny`.

---

## Why this matters for AI agents

A human running commands has natural friction — typing, reading the screen, switching windows. An AI agent has none of that. It can run dozens of tool calls in the time it takes you to read the previous one. That changes the risk profile in three specific ways:

1. **Speed makes mistakes irreversible.** A human might catch "wait, that's prod" mid-keystroke. An agent doesn't pause. By the time you read the transcript, the damage is done. `deny` rules exist because some mistakes can't be undone with `git revert`.

2. **Autonomy creates surface area.** The more an agent can do without asking, the larger the surface area for a misunderstanding, a bad suggestion from training data, or a [prompt injection](https://owasp.org/www-project-top-10-for-large-language-model-applications/) in a file or web page it reads. `ask` rules keep a human in the loop on state-changing actions; `deny` rules cap the worst case.

3. **Confidence is not correctness.** An agent that confidently runs `rm -rf node_modules/` followed by a confident "all set!" is no different from an agent that confidently drops a production table. Permissions are a check on the agent's output that doesn't depend on the agent being right.

The goal is not to make the agent useless — it's to make the **cheap, reversible** operations friction-free and the **expensive, irreversible** ones impossible (or at least intentional).

---

## Editing the configuration

To change permissions, edit `.claude/settings.json`. The schema reference is at `https://json.schemastore.org/claude-code-settings.json` (already referenced in the file's `$schema` field for IDE autocomplete).

Pattern syntax for `Bash` rules:

- Exact match: `Bash(git status)`
- Prefix wildcard: `Bash(git diff *)` — matches `git diff`, `git diff HEAD`, `git diff main..feature`, etc.
- Argument wildcard: `Bash(kubectl * --context prod*)` — `*` can appear mid-pattern.

Pattern syntax for file tools (`Read`, `Edit`, `Write`):

- Repo-relative: `Read(./.env)`
- Recursive glob: `Read(**/secrets/**)`

Pattern syntax for `WebFetch`:

- Domain match: `WebFetch(domain:github.com)` — allows fetches to github.com and its paths. Add subdomains explicitly (`WebFetch(domain:raw.githubusercontent.com)`) — there's no wildcard for subdomains.

After editing, the new rules take effect immediately for new tool calls.

---

## Related

- `.cursor/rules/000-core-safety.mdc` — high-level safety rules that apply regardless of the permission system.
- `.cursor/rules/100-git-workflow.mdc` — git-specific rules that mirror many of the `deny` entries here.
- `.cursor/rules/300-security.mdc` — secrets and infrastructure rules that motivate the `Read(**/.env)` and deploy-command denials.
