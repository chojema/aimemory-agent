<!-- AIMEMORY-INSTRUCTIONS:START -->
# AIMemory Instructions

This repository uses `AIMemory/` as shared project memory for coding agents.

Before starting any task:
1. Read `AIMemory/PROTOCOL.md`.
2. Read `AIMemory/PROJECT_OVERVIEW.md`.
3. Read the recent tail of `AIMemory/work.log`.
4. Check `AIMemory/INDEX.md` and `AIMemory/handoffs/` if the user asks to continue, review, or receive another agent's work.

During work:
- Preserve recorded project decisions unless the user explicitly changes them.
- Keep generated markdown under `AIMemory/` unless the file is a harness-required instruction file.
- Avoid parallel edits to the same source files unless roles are clearly separated.
- During long tasks, append `CHECKPOINT` events to `AIMemory/work.log` at meaningful milestones.
- If context pressure becomes high, append `CONTEXT_PRESSURE`, prepare a handoff file, and recommend continuing in another agent or a fresh session.

After completing work:
1. Append a concise event to `AIMemory/work.log`.
2. Record changed files, decisions, unresolved issues, and next steps.
3. Update `AIMemory/INDEX.md` when new durable topics or handoffs are added.
4. Update `AIMemory/PROJECT_OVERVIEW.md` when project direction, stack, or locked-in decisions change.
5. Create a handoff file under `AIMemory/handoffs/` when another agent should continue the task.

Do not ignore `AIMemory/` unless the user explicitly says this is a one-off task.
<!-- AIMEMORY-INSTRUCTIONS:END -->
