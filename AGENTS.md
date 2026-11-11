# AGENTS.md

One file of project facts for coding agents that read `AGENTS.md`
(Cursor, OpenAI Codex, Claude Code, and others). Keep it short: commands,
conventions, and lines the agent must not cross.

If this repo also has a `CLAUDE.md`, start that file with `@AGENTS.md` or
Claude Code will skip this file by default. See `docs/CLAUDE-MD-GOTCHA.md`.

Fill in the bracketed notes, then delete the stack sections you don't use.

## What this project is

[One or two sentences: what it does and who it's for.]

Layout:

- `[dir/]`: [what lives here]
- `[dir/]`: [what lives here]

## Commands

Use these exact commands. If one fails because the project changed, read the
manifest (`package.json`, `pyproject.toml`, `go.mod`, Makefile, etc.) and say
so. Don't invent a substitute.

| Task | Command |
| --- | --- |
| Install | [e.g. pnpm install] |
| Dev | [e.g. pnpm dev] |
| Typecheck / build | [e.g. pnpm build] |
| Lint / format | [e.g. pnpm lint] |
| Tests (all) | [e.g. pnpm test] |
| Tests (one file) | [e.g. pnpm test path/to/file] |

## Definition of done

A change is done when all of these are true:

1. It does what was asked, and nothing else (no drive-by refactors).
2. Typecheck, lint, and the relevant tests were run, not assumed.
3. New behavior has a test, or the reply says plainly why it doesn't.
4. The reply lists what changed, what was run, and anything left unverified.

## Working rules

- Read the surrounding code before editing. Match the patterns already in that file.
- Prefer the smallest change that solves the problem. Ask before a rewrite.
- Don't add a dependency without saying why, and naming the alternative you skipped.
- Don't change public APIs, schemas, or config formats unless the task says so.
- Never hard-code secrets or personal data. Read them from the environment.
- Don't edit generated or vendored files (`dist/`, `build/`, `vendor/`, `*.gen.ts`, and so on).
- If these instructions conflict with the current task, stop and ask which one wins.

## Boundaries (ask first)

- Deleting files, migrations, or data
- Auth, payments, billing, or permissions
- CI, deploy, or infrastructure config
- Commands that hit production or paid external APIs

## Working alongside other agents

Several agents, or several sessions, may be in this repo at once.

- Stay in the files the task names. If you need something outside that scope, say so first.
- Use your own branch or git worktree. Don't commit straight to the default branch.
- Don't reformat files you didn't otherwise change.
- Before you stop, leave a short handoff: what's done, what's left, what you ran, open questions.
- If you find someone else's half-finished work, leave it alone and mention it.

## Git

- One logical change per commit. Don't commit `.env` files, secrets, or large binaries.
- Follow the repo's existing commit style (look at `git log`).

## Project-specific gotchas

Add an entry when an agent makes the same mistake twice. Remove entries that no longer apply.

- [e.g. `scripts/seed.ts` wipes the dev database; never point it at production]

---

<!-- Keep the one stack section that matches this repo. Delete the other two. -->

## Stack: Next.js (App Router)

- Components are Server Components unless the file starts with `"use client"`. Add that directive only in the smallest leaf that needs state, effects, or browser APIs.
- Don't import server-only modules (database clients, secrets, `fs`) into a client component.
- Fetch data in Server Components or route handlers, not in `useEffect`, unless the data is truly client-only.
- Mutations go through Server Actions or `app/**/route.ts`. Validate input on the server even if the form already validated it.
- After a mutation, revalidate the path or tag that changed. Don't turn caching off globally to paper over a stale page.
- Only `NEXT_PUBLIC_*` env vars reach the browser. Never put secrets in them.
- Check the installed Next.js version in `package.json` before using an API; App Router details change between majors.
- Don't add `"use client"` to a layout or page just to silence an error. Find the component that needs it.

## Stack: Python + FastAPI

- Request and response bodies are Pydantic models. Set `response_model` (or a return annotation) so responses are validated.
- Use `Depends` for DB sessions, auth, and settings. Don't open clients at import time inside route modules.
- Raise `HTTPException` (or the project's domain error) with a real status code. Don't return HTTP 200 with an error payload.
- Keep routers thin. Business logic belongs in functions you can test without HTTP.
- In `async def` endpoints, use async I/O. Blocking calls belong in a plain `def` endpoint or a thread pool. Don't `time.sleep` in async code.
- Settings come from the environment (for example pydantic-settings). No secrets in source.
- Schema changes go through migrations. Don't edit a migration that has already been applied; add a new one.
- Type hints on public functions. Tests use the app's test client and dependency overrides, not live external services.

## Stack: Go

- Handle every error. Wrap with `fmt.Errorf("doing x: %w", err)` and check with `errors.Is` / `errors.As`.
- Don't `panic` for expected failures. Log an error or return it, not both.
- Functions that do I/O take `ctx context.Context` as the first parameter and respect cancellation.
- Every goroutine has an owner and a way to stop (`errgroup` or `sync.WaitGroup`). Don't leak goroutines.
- Protect shared state with a mutex, or confine it to one goroutine. Run `go test -race` on concurrent code.
- Accept interfaces, return concrete types. Keep `package main` thin; logic lives under `internal/` (or the repo's existing layout).
- Don't add a module for something the standard library already does well (HTTP, JSON, `log/slog`).
- Table-driven tests with `t.Run`. Don't hit real network services in unit tests; use `httptest` and fakes.
- Before calling a change done: `gofmt` / `goimports`, `go vet ./...`, `go test ./...`, plus the repo's linter if it has one.
