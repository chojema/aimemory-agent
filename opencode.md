<!-- AIMEMORY-INSTRUCTIONS:START -->
# AIMemory Instructions for OpenCode

This project uses `AIMemory/` as shared working memory for Codex, Claude Code, Antigravity CLI, OpenCode, and other coding agents.

## Classify the request before reading AIMemory

**Query mode** — user asks only for information (status, file content, count, summary, opinion, assessment):
- Read only the files directly needed to answer the question.
- Do not run the session start checklist.
- Report the answer. Stop. Do not predict or begin the next action.
- Wait for the user to decide what to do next.

**Status and opinion rule** — user asks about status, progress, opinions, or assessments:
- Answer with the observed facts or your assessment. Stop.
- Do not proceed to any next action without an explicit instruction from the user.

**Task mode** — user asks the agent to create, modify, delete, move, or execute something:
- Run the session start checklist below before touching files.

## Session start checklist (task mode only)

1. Read `AIMemory/PROTOCOL.md`.
2. Read `AIMemory/PROJECT_OVERVIEW.md`.
3. Read the recent entries in `AIMemory/work.log`.
4. If asked to receive or review another agent's work, inspect `AIMemory/handoffs/`.

## During work

- Follow the protocol in `AIMemory/PROTOCOL.md`.
- Keep project memory concise and durable.
- Do not duplicate the full protocol in this file.
- Put new markdown artifacts under `AIMemory/` unless they are required root instruction files.
- During long tasks, write `CHECKPOINT` events to `AIMemory/work.log` at meaningful milestones.
- If context pressure becomes high, write `CONTEXT_PRESSURE`, prepare a handoff file, and recommend continuing in another agent or a fresh session.

## After work

1. Append a concise event to `AIMemory/work.log`.
2. Record changed files, decisions, unresolved issues, and recommended next actions.
3. Update `AIMemory/INDEX.md` and `AIMemory/PROJECT_OVERVIEW.md` when needed.
4. Write handoff files to `AIMemory/handoffs/`.
<!-- AIMEMORY-INSTRUCTIONS:END -->
