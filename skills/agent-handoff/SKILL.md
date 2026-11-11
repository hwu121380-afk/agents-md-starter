---
name: agent-handoff
description: Writes a short handoff note so another agent, a new session, or a teammate can continue without re-reading the whole conversation. Use when ending a session, before switching tools, or when asked to hand off or summarize where things stand.
metadata:
  category: planning
---

# Agent handoff

Write the note you would want if you started cold on this task in ten minutes.

## Steps

1. Gather facts. Run `git status`, `git log --oneline -15`, and `git diff --stat` against the default branch. Re-run the verify command (tests, typecheck, or build) and record the real result.
2. State the goal and the current state: what's done, what's half-done, what has not started.
3. List decisions and why, especially ones a fresh agent would be tempted to undo.
4. List dead ends: approaches tried and dropped, with the reason.
5. Give the next three actions, concrete enough to start immediately (file, function, command).
6. Note open questions for the user.
7. Save the note where the user wants. Default: `HANDOFF.md` at the worktree root. Ask before committing it.

## Rules

- Label anything you didn't verify as unverified.
- Paths are relative to the repo root. Commands should be copy-pasteable.
- No secrets, tokens, or personal data.
- Keep it under about 60 lines. Link to files instead of pasting large code.

## Output sections

Goal · Status · Verify result · Decisions · Dead ends · Next actions · Open questions · Files touched

## Done when

The note exists, the verify result came from a command run in this session, and the next action is unambiguous.
