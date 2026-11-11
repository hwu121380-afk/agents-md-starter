# The CLAUDE.md gotcha

This is a loading-order issue in Claude Code, not a bug in AGENTS.md itself.
Confirmed against Claude Code's memory docs (read 11 November 2026):
https://code.claude.com/docs/en/memory

## What happens

By default, Claude Code reads `AGENTS.md` **only when there is no** `CLAUDE.md`,
`.claude/CLAUDE.md`, or `CLAUDE.local.md` in the working directory or any
directory above it.

So if you keep a shared `AGENTS.md` for Cursor and Codex, then add a short
`CLAUDE.md` for Claude-specific notes, Claude Code will read the `CLAUDE.md`
and skip `AGENTS.md`. Your shared rules never reach that session.

`CLAUDE.local.md` counts too. A personal, often-gitignored file is enough to
hide `AGENTS.md` for you, even if the rest of the team is fine.

A sentence in `CLAUDE.md` like "also read AGENTS.md" is not an import. Claude
only sees that file if it decides to open it.

## The one-line fix

Keep `@AGENTS.md` as the **first line** of `CLAUDE.md` (this repo's `CLAUDE.md`
does that). `@path` is Claude Code's import syntax; the imported file is
expanded into context. Put Claude-only notes below the import.

```markdown
@AGENTS.md

## Claude Code notes
- Use plan mode before changes that touch billing.
```

Then run `/context all` or `/memory` in a Claude Code session and confirm
both files are listed.

## Other options

- If you have no Claude-specific notes, you can delete `CLAUDE.md` and let
  Claude Code read `AGENTS.md` directly (Claude Code v2.1.277 or later).
- To keep a `CLAUDE.md` or `CLAUDE.local.md` and also load `AGENTS.md`
  without an import, set Project instructions in `/config` to
  `claude-md-and-agents-md`.
- A symlink from `CLAUDE.md` to `AGENTS.md` can work, but the import is
  simpler to review and is the safer default on Windows (symlink checkout
  needs `core.symlinks`).

## Related pitfalls (same family of problems)

- **Cursor** reads `AGENTS.md` at the project root, and project rules in
  `.cursor/rules/*.mdc` with YAML frontmatter. Plain `.md` files in that
  folder are ignored.
- **Codex** reads `AGENTS.md` (and `AGENTS.override.md` in the same
  directory). It does not read `CLAUDE.md` or Cursor `.mdc` files by default.
  Keep shared guidance in `AGENTS.md`, not only in tool-specific rule files.
- Nested `AGENTS.md` files in a monorepo package are not read by Claude Code
  by default if a root `CLAUDE.md` exists. Add a `CLAUDE.md` next to the
  nested `AGENTS.md` that starts with `@AGENTS.md`.

Sources (re-check after tool updates):

- Claude Code memory: https://code.claude.com/docs/en/memory
- Cursor rules: https://cursor.com/docs/context/rules
- Codex AGENTS.md: https://developers.openai.com/codex/guides/agents-md
- AGENTS.md format: https://agents.md
