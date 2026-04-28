<!-- AIMEMORY-INSTRUCTIONS:START -->
# AIMemory Instructions for Claude Code

This project uses `AIMemory/` as shared working memory across multiple AI coding agents.

At the beginning of each task:
1. Read `AIMemory/PROTOCOL.md`.
2. Read `AIMemory/PROJECT_OVERVIEW.md`.
3. Read the recent tail of `AIMemory/work.log`.
4. Check `AIMemory/handoffs/` for open handoffs when the user asks to continue another agent's work.

While working:
- Follow `AIMemory/PROTOCOL.md`.
- Do not overwrite another agent's active work.
- Keep durable notes, plans, reviews, and handoffs under `AIMemory/`.
- Use `CLAUDE.md` only for Claude Code project instructions.
- During long tasks, write `CHECKPOINT` events to `AIMemory/work.log` at meaningful milestones.
- If context pressure becomes high, write `CONTEXT_PRESSURE`, create or recommend a handoff, and tell the user that another agent or fresh session can continue safely.

At the end of each task:
1. Append a structured summary to `AIMemory/work.log`.
2. Include changed files, decisions, open risks, and next steps.
3. Update `AIMemory/INDEX.md` for new topics.
4. Update `AIMemory/PROJECT_OVERVIEW.md` for durable project changes.
5. Create a handoff file in `AIMemory/handoffs/` if another agent should continue.
<!-- AIMEMORY-INSTRUCTIONS:END -->
