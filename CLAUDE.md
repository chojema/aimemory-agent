<!-- AIMEMORY-INSTRUCTIONS:START -->
# AIMemory Instructions for Claude Code

This project uses `.AIMemory/` as shared working memory across multiple AI coding agents.

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

1. Read `.AIMemory/PROTOCOL.md`.
2. Read `.AIMemory/PROJECT_OVERVIEW.md`.
3. Read the recent tail of `.AIMemory/work.log`.
4. Check `.AIMemory/handoffs/` for open handoffs when the user asks to continue another agent's work.

## During work

- Follow `.AIMemory/PROTOCOL.md`.
- Do not overwrite another agent's active work.
- Keep durable notes, plans, reviews, and handoffs under `.AIMemory/`.
- Use `CLAUDE.md` only for Claude Code project instructions.
- During long tasks, write `CHECKPOINT` events to `.AIMemory/work.log` at meaningful milestones.
- If context pressure becomes high, write `CONTEXT_PRESSURE`, create or recommend a handoff, and tell the user that another agent or fresh session can continue safely.

## After work

1. Append a structured summary to `.AIMemory/work.log`.
2. Include changed files, decisions, open risks, and next steps.
3. Update `.AIMemory/INDEX.md` for new topics.
4. Update `.AIMemory/PROJECT_OVERVIEW.md` for durable project changes.
5. Create a handoff file in `.AIMemory/handoffs/` if another agent should continue.
<!-- AIMEMORY-INSTRUCTIONS:END -->
