# agents-md-starter

A small, copy-and-edit starter for repos that run more than one coding agent.

It gives you:

- A lite `AGENTS.md` (shared project facts, plus optional sections for Next.js, FastAPI, and Go)
- A 3-line `CLAUDE.md` that imports it, so Claude Code actually sees the same file
- A short note on the loading gotcha that hides `AGENTS.md`
- Three sample `SKILL.md` workflows: PR review, flaky-test diagnosis, and a session handoff

No installer, no generator, no dependencies. Copy the files into your repo and fill in the bracketed notes.

## Why a single AGENTS.md

Cursor, OpenAI Codex, and several other tools read `AGENTS.md` at the project root. Claude Code can read it too, but **only if** there is no `CLAUDE.md` or `CLAUDE.local.md` in the working directory or above it (default as of the Claude Code memory docs, November 2026).

That is the usual trap: you write a solid `AGENTS.md`, then add a `CLAUDE.md` for Claude-specific notes, and Claude Code starts ignoring the shared file. The fix is one line at the top of `CLAUDE.md`:

```markdown
@AGENTS.md
```

`@path` is an import. A sentence that says "please read AGENTS.md" is not. Details, including the `CLAUDE.local.md` variant and a `/config` alternative, are in [`docs/CLAUDE-MD-GOTCHA.md`](docs/CLAUDE-MD-GOTCHA.md).

## Quick start

1. Copy `AGENTS.md` and `CLAUDE.md` to the root of your project.
2. Fill in the project description, command table, and conventions. Delete the two stack sections you don't use (or delete all three and write your own).
3. In a Claude Code session, run `/memory` or `/context all` and confirm both files are listed.
4. If you use Cursor project rules, remember they have to be `.cursor/rules/*.mdc` with YAML frontmatter. Plain `.md` files in that folder are ignored. Codex reads `AGENTS.md` only, so don't keep shared guidance solely in Cursor or Claude rule files.

This starter does not generate `.cursor/rules` or `.claude/rules` for you. If you already have those, leave them in place and keep `AGENTS.md` as the source of shared facts.

## Sample skills

Skills are on-demand procedures. Rules (`AGENTS.md`, `CLAUDE.md`) load every session; a skill loads when you call it, or when the agent decides the task matches its description.

| Skill | When to use it |
| --- | --- |
| [`skills/pr-review/`](skills/pr-review/SKILL.md) | Review a PR, a branch, or a diff. Findings come back ranked, with file:line, and only checks that were actually run. |
| [`skills/fix-flaky-test/`](skills/fix-flaky-test/SKILL.md) | A test that fails intermittently. Reproduce it, find the nondeterminism, fix the cause. No retry band-aids. |
| [`skills/agent-handoff/`](skills/agent-handoff/SKILL.md) | Ending a session or switching agents. Writes a short note a fresh session can start from. |

To install one, copy its folder (the folder name must match the `name:` field in `SKILL.md`) into the location your tool reads:

| Tool | Project folder |
| --- | --- |
| Claude Code | `.claude/skills/<name>/` |
| Cursor (2.4+) | `.cursor/skills/<name>/` or `.agents/skills/<name>/` (Cursor also reads `.claude/skills/`) |
| OpenAI Codex | `.agents/skills/<name>/` |

Then call it by name (`/pr-review`, or whatever your tool uses) or describe the task. Auto-selection depends on the tool and model; calling it by name is the reliable path.

These three are samples, not a complete workflow pack. Each one is a single `SKILL.md` you can edit.

## What to put in AGENTS.md (and what to leave out)

Keep facts the agent can't guess: real commands, the definition of done, ask-first boundaries, and the mistakes this repo has already seen twice.

Leave multi-step procedures (how to review a PR, how to hunt a flake) in skills, so they don't sit in context on every turn.

Instruction files guide agents. They don't enforce anything. Use hooks, linters, permissions, and CI when a rule has to be hard.

## License

MIT. See [LICENSE](LICENSE).

## Full version

This repo is a free starter. It is not a substitute for the paid kits, and it does not include their tools or the rest of their content.

If you want the fuller set:

- **[Agent Rules Kit](https://payhip.com/b/mB0L3)** — $17. One `AGENTS.md` as the source of truth, a `CLAUDE.md` that imports it, file-scoped rules for 12 stacks (Next.js, React + Vite, Node.js TypeScript API, FastAPI, Django, Python data/ML, Go, Rust, Rails, SvelteKit, React Native + Expo, monorepo) and 6 topics, a `compose.py` script that writes Cursor `.mdc` rules and Claude Code rules from the same source, and a checker that flags unfilled placeholders, broken `.mdc` YAML, plain `.md` files Cursor ignores, a `CLAUDE.md` that doesn't import `AGENTS.md`, a `CLAUDE.local.md` that hides `AGENTS.md`, and files over each tool's size guidance.
- **[Agent Skill Pack](https://payhip.com/b/S3RJk)** — $17. 24 `SKILL.md` workflows covering plan, review, test, debug, release, docs, git, and build-in-public, plus an installer that won't overwrite existing skills unless you pass `--force`, a linter for skills you write yourself, and prompt versions for tools that don't load skills.

Each works on its own. Neither is affiliated with or endorsed by Anthropic, Anysphere (Cursor), or OpenAI. Tool behavior changes; the docs in each kit link the sources so you can re-check.
