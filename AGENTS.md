<!-- AIMEMORY-INSTRUCTIONS:START -->
# AIMemory Instructions

This repository uses `.AIMemory/` as shared project memory for coding agents.

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
4. Check `.AIMemory/INDEX.md` and `.AIMemory/handoffs/` if the user asks to continue, review, or receive another agent's work.

## During work

- Preserve recorded project decisions unless the user explicitly changes them.
- Keep generated markdown under `.AIMemory/` unless the file is a harness-required instruction file.
- Avoid parallel edits to the same source files unless roles are clearly separated.
- During long tasks, append `CHECKPOINT` events to `.AIMemory/work.log` at meaningful milestones.
- If context pressure becomes high, append `CONTEXT_PRESSURE`, prepare a handoff file, and recommend continuing in another agent or a fresh session.

## After work

1. Append a concise event to `.AIMemory/work.log`.
2. Record changed files, decisions, unresolved issues, and next steps.
3. Update `.AIMemory/INDEX.md` when new durable topics or handoffs are added.
4. Update `.AIMemory/PROJECT_OVERVIEW.md` when project direction, stack, or locked-in decisions change.
5. Create a handoff file under `.AIMemory/handoffs/` when another agent should continue the task.

Do not ignore `.AIMemory/` unless the user explicitly says this is a one-off task.
<!-- AIMEMORY-INSTRUCTIONS:END -->
