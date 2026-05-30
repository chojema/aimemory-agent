# AIMemory Agent Setup

You are an agentic coding assistant working inside the user's project folder.

Your task is to install or re-engage a shared project memory system named `AIMemory`, and to wire this project so that Codex, Claude Code, Antigravity CLI, OpenCode, and other file-aware coding agents naturally read and update that memory on future turns.

This prompt assumes you have:
- filesystem read access
- filesystem write access
- shell execution access

If you do not have filesystem read/write access, stop and tell the user that this setup must be run from an agentic coding environment such as Codex CLI, Claude Code, Antigravity CLI, Cursor agent mode, Aider, Cline, Continue, OpenCode, or a similar tool.

---

## Goal

Set up this project so that multiple AI coding agents can collaborate in the same working folder without losing context.

The project memory must support:

1. shared task history
2. project overview for new agents
3. cross-agent handoff
4. recent-event memory
5. archived older memory
6. agent-specific project instruction files
7. automatic checkpoints during long tasks
8. context-pressure detection and handoff recommendation
9. query-mode discipline: skip the full session start for status queries, simple file checks, counts, and informational questions; read only the files directly needed to answer
10. status-first reporting: when the user asks about status, state, or opinions, report the facts and stop; do not pre-empt or begin the next action without an explicit instruction

The key improvement in this setup is that the bootstrap process must not only create `AIMemory/`. It must also create or update the root-level instruction files that common agent harnesses automatically read:

- `AGENTS.md` for Codex and other AGENTS-compatible tools
- `CLAUDE.md` for Claude Code
- `GEMINI.md` for Antigravity CLI
- `opencode.md` for OpenCode

These root files must stay short. They should point agents to `AIMemory/PROTOCOL.md` rather than duplicating the full protocol.

---

## Required directory structure

Create the following structure at the project root if it does not already exist:

```text
AIMemory/
├─ PROTOCOL.md
├─ INDEX.md
├─ PROJECT_OVERVIEW.md
├─ work.log
├─ archive/
├─ cold/
└─ handoffs/
```

If these files already exist, do not overwrite them. Read them, preserve them, and append a `RE_ENGAGED` event to `AIMemory/work.log`.

---

## Agent identity

Before changing files, identify yourself as accurately as possible.

Use this format:

```text
Model-id: <lowercase-kebab-case-model-id>
Vendor: <Anthropic | OpenAI | Google | xAI | Mistral | DeepSeek | Meta | Alibaba | other>
Harness: <Claude Code | Codex CLI | Antigravity CLI | Cursor | Aider | Cline | Continue | OpenCode | Windsurf | other>
Capabilities: <filesystem-read, filesystem-write, shell-exec, web-fetch, web-search, code-sandbox, image-input, image-output, subagent-spawn>
Strengths: <one short line>
Context-window: <tokens if known, otherwise unknown>
```

If the exact model name is uncertain, do not guess. Use:

```text
<vendor>-unknown-<YYYY-MM>
```

and note the uncertainty in `work.log`.

---

## Setup tasks

Perform the following tasks in order.

### Task 1. Confirm project root

Run a directory listing command and confirm that you are in the intended project root.

Examples:

```bash
pwd
ls -la
```

On Windows PowerShell:

```powershell
Get-Location
Get-ChildItem
```

Do not install `AIMemory/` in the user's home folder unless the user explicitly says the home folder is the project root.

---

### Task 2. Create AIMemory structure

Create the directory structure if missing.

POSIX-compatible shell:

```bash
mkdir -p AIMemory/archive AIMemory/cold AIMemory/handoffs
```

PowerShell:

```powershell
New-Item -ItemType Directory -Force -Path AIMemory, AIMemory/archive, AIMemory/cold, AIMemory/handoffs
```

---

### Task 3. Create `AIMemory/PROTOCOL.md`

If `AIMemory/PROTOCOL.md` does not exist, create it with the content in the section "Protocol file content" below.

If it already exists, do not overwrite it.

---

### Task 4. Create `AIMemory/INDEX.md`

If `AIMemory/INDEX.md` does not exist, create it with this content:

```markdown
# AIMemory Index

This file maps topics to the memory files that contain relevant project history.

Agents should use this file to decide which archived memory files to read instead of loading every old log.

## Topic index

| Topic | File | Notes |
|---|---|---|

## Open handoffs

| Handoff | From | To | Status | Notes |
|---|---|---|---|---|

## Recent archive files

| Date | File | Topics |
|---|---|---|
```

If it already exists, preserve it.

---

### Task 5. Create `AIMemory/PROJECT_OVERVIEW.md`

If `AIMemory/PROJECT_OVERVIEW.md` does not exist, create it with this content:

```markdown
# Project Overview

This file is the short onboarding brief for any AI agent joining this project.

## Project purpose

Unknown. Fill this in after inspecting the project or asking the user.

## Technology stack

Unknown.

## Current state

AIMemory has been initialized. Project details still need to be summarized.

## Locked-in decisions

None recorded yet.

## Current work

None recorded yet.

## Open handoffs

None recorded yet.

## Important constraints

- Read `AIMemory/PROTOCOL.md` before working.
- Read recent entries in `AIMemory/work.log` before changing files.
- Record meaningful work in `AIMemory/work.log`.
- Create checkpoints during long tasks so another agent can continue safely.
```

If the file already exists, preserve it.

---

### Task 6. Create `AIMemory/work.log`

If `AIMemory/work.log` does not exist, create it with this content:

```text
# AIMemory work.log
#
# Append-only event log. Newest events go at the bottom.
#
# Event header:
# ### YYYY-MM-DD HH:MM | <model-id> | <EVENT>
#
# Core events:
# PROJECT_BOOTSTRAPPED, RE_ENGAGED, PROMPT, WORK_START, WORK_END,
# FILES_CREATED, FILES_MODIFIED, FILES_MOVED, FILES_DELETED,
# DECISION, NOTE, HANDOFF, HANDOFF_RECEIVED, HANDOFF_CLOSED,
# REVIEW_RESPONSE, CHECKPOINT, CONTEXT_PRESSURE, HANDOFF_RECOMMENDED,
# BLOCKER, CORRECTION
#
# Read the recent tail before every new task.
# Do not edit old entries in place. Append corrections as new events.
# See AIMemory/PROTOCOL.md for the full protocol.
# ============================================================
```

If it already exists, preserve it.

---

### Task 7. Append bootstrap or re-engagement event

Append exactly one event to `AIMemory/work.log`.

If this is the first installation, append `PROJECT_BOOTSTRAPPED`.

If files already existed, append `RE_ENGAGED`.

Use the current local date and time.

Format:

```text
### YYYY-MM-DD HH:MM | <model-id> | PROJECT_BOOTSTRAPPED
Vendor: <vendor>
Harness: <harness>
Capabilities: <capabilities>
Strengths: <strengths>
Context-window: <context-window>
Notes: AIMemory initialized. Root agent instruction files will point to AIMemory/PROTOCOL.md.
```

or:

```text
### YYYY-MM-DD HH:MM | <model-id> | RE_ENGAGED
Vendor: <vendor>
Harness: <harness>
Capabilities: <capabilities>
Strengths: <strengths>
Context-window: <context-window>
Notes: Existing AIMemory found. Preserved existing files and re-engaged with the protocol.
```

Use a single append operation. Do not open the log, edit old text, and save it.

---

### Task 8. Create or update root agent instruction files

Create or update the following files at the project root:

```text
AGENTS.md
CLAUDE.md
GEMINI.md
opencode.md
```

These files must be short pointers to `AIMemory/PROTOCOL.md`.

If a file does not exist, create it.

If a file already exists, preserve its existing content and append the managed block below unless the managed block already exists.

Use these exact managed block markers:

```markdown
<!-- AIMEMORY-INSTRUCTIONS:START -->
...
<!-- AIMEMORY-INSTRUCTIONS:END -->
```

If the block already exists, update only the text inside the block. Do not rewrite unrelated content.

#### Managed block for `AGENTS.md`

```markdown
<!-- AIMEMORY-INSTRUCTIONS:START -->
# AIMemory Instructions

This repository uses `AIMemory/` as shared project memory for coding agents.

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
3. Read the recent tail of `AIMemory/work.log`.
4. Check `AIMemory/INDEX.md` and `AIMemory/handoffs/` if the user asks to continue, review, or receive another agent's work.

## During work

- Preserve recorded project decisions unless the user explicitly changes them.
- Keep generated markdown under `AIMemory/` unless the file is a harness-required instruction file.
- Avoid parallel edits to the same source files unless roles are clearly separated.
- During long tasks, append `CHECKPOINT` events to `AIMemory/work.log` at meaningful milestones.
- If context pressure becomes high, append `CONTEXT_PRESSURE`, prepare a handoff file, and recommend continuing in another agent or a fresh session.

## After work

1. Append a concise event to `AIMemory/work.log`.
2. Record changed files, decisions, unresolved issues, and next steps.
3. Update `AIMemory/INDEX.md` when new durable topics or handoffs are added.
4. Update `AIMemory/PROJECT_OVERVIEW.md` when project direction, stack, or locked-in decisions change.
5. Create a handoff file under `AIMemory/handoffs/` when another agent should continue the task.

Do not ignore `AIMemory/` unless the user explicitly says this is a one-off task.
<!-- AIMEMORY-INSTRUCTIONS:END -->
```

#### Managed block for `CLAUDE.md`

```markdown
<!-- AIMEMORY-INSTRUCTIONS:START -->
# AIMemory Instructions for Claude Code

This project uses `AIMemory/` as shared working memory across multiple AI coding agents.

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
3. Read the recent tail of `AIMemory/work.log`.
4. Check `AIMemory/handoffs/` for open handoffs when the user asks to continue another agent's work.

## During work

- Follow `AIMemory/PROTOCOL.md`.
- Do not overwrite another agent's active work.
- Keep durable notes, plans, reviews, and handoffs under `AIMemory/`.
- Use `CLAUDE.md` only for Claude Code project instructions.
- During long tasks, write `CHECKPOINT` events to `AIMemory/work.log` at meaningful milestones.
- If context pressure becomes high, write `CONTEXT_PRESSURE`, create or recommend a handoff, and tell the user that another agent or fresh session can continue safely.

## After work

1. Append a structured summary to `AIMemory/work.log`.
2. Include changed files, decisions, open risks, and next steps.
3. Update `AIMemory/INDEX.md` for new topics.
4. Update `AIMemory/PROJECT_OVERVIEW.md` for durable project changes.
5. Create a handoff file in `AIMemory/handoffs/` if another agent should continue.
<!-- AIMEMORY-INSTRUCTIONS:END -->
```

#### Managed block for `GEMINI.md`

```markdown
<!-- AIMEMORY-INSTRUCTIONS:START -->
# AIMemory Instructions for Antigravity CLI

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
```

#### Managed block for `opencode.md`

```markdown
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
```

---

### Task 9. Confirm setup

After completing the setup, reply with:

```text
AIMemory setup complete.

Model-id: <model-id>
Vendor: <vendor>
Harness: <harness>
Capabilities: <capabilities>

Created or verified:
- <absolute path>/AIMemory/PROTOCOL.md
- <absolute path>/AIMemory/INDEX.md
- <absolute path>/AIMemory/PROJECT_OVERVIEW.md
- <absolute path>/AIMemory/work.log
- <absolute path>/AIMemory/archive/
- <absolute path>/AIMemory/cold/
- <absolute path>/AIMemory/handoffs/
- <absolute path>/AGENTS.md
- <absolute path>/CLAUDE.md
- <absolute path>/GEMINI.md
- <absolute path>/opencode.md

From now on, I will read AIMemory before work, update it after work, and create checkpoints during long tasks.
```

Do not claim files were created if they already existed. Say "created or verified" unless you are certain.

---

# Protocol file content

Write the following to `AIMemory/PROTOCOL.md` if the file does not already exist.

```markdown
# AIMemory Protocol

This protocol applies to every AI coding agent working in this project.

`AIMemory/` is the shared project memory. It exists so that multiple agents can work in the same folder, hand work off to one another, and preserve important context across sessions.

---

## 1. Files and roles

### Required files

```text
AIMemory/
├─ PROTOCOL.md
├─ INDEX.md
├─ PROJECT_OVERVIEW.md
├─ work.log
├─ archive/
├─ cold/
└─ handoffs/
```

### File roles

| File or folder | Role |
|---|---|
| `PROTOCOL.md` | Shared rules for all agents |
| `PROJECT_OVERVIEW.md` | Short onboarding brief for new agents |
| `work.log` | Recent append-only task history |
| `INDEX.md` | Topic index for finding older or related memory |
| `archive/` | Older daily or task logs |
| `cold/` | Monthly or long-term summaries |
| `handoffs/` | Structured handoff files between agents |

---

## 2. Root instruction files

Some agent harnesses automatically read root-level instruction files.

This project may contain:

| File | Intended reader |
|---|---|
| `AGENTS.md` | Codex and AGENTS-compatible tools |
| `CLAUDE.md` | Claude Code |
| `GEMINI.md` | Antigravity CLI |
| `opencode.md` | OpenCode |

These files should stay short. They should point to this protocol rather than duplicating it.

Do not place long project notes in root instruction files. Put durable notes under `AIMemory/`.

---

## 3. Start-of-task checklist

Before loading any AIMemory files, classify the request.

### 3.1 Query mode

A request is in query mode when it asks only for information without requiring any change:

- current status, state, or progress
- count, listing, or summary
- content of a specific file
- opinion, recommendation, or assessment
- any yes/no or factual answer

In query mode:
1. Read only the files directly needed to answer the question.
2. Skip the full session start checklist.
3. Report the answer. Stop there.
4. Do not predict or begin the next action. Wait for the user to decide.

Query-mode examples:
- "How many handoff files are there?"
- "What does PROJECT_OVERVIEW.md say about the stack?"
- "Is there an open handoff?"
- "What is the current status?"
- "What do you think about this approach?"

### 3.2 Status and opinion rule

When the user asks about status, progress, opinions, or assessments:
- Answer with the observed facts or your assessment.
- Stop. Do not proceed to the next logical action.
- Do not assume the user wants you to continue working.
- Wait for an explicit instruction before taking any follow-up action.

Taking unsolicited action after a status question wastes tokens, changes files the user did not authorize, and removes the user's control over what happens next.

### 3.3 Task mode session start

A request is in task mode when it asks the agent to create, modify, delete, move, or execute something.

Before changing any files, the active agent must:

1. Read `AIMemory/PROTOCOL.md`.
2. Read `AIMemory/PROJECT_OVERVIEW.md`.
3. Read the recent tail of `AIMemory/work.log`.
4. Check for an open or relevant handoff in `AIMemory/handoffs/` if the task involves continuation, review, or receiving another agent's work.
5. Check `AIMemory/INDEX.md` when older context may matter.

If the user explicitly says not to log the turn, do not log it.

---

## 4. work.log rules

`AIMemory/work.log` is an append-only event log.

Never edit old entries in place.

If a previous entry was wrong, append a `CORRECTION` event.

Use this event header:

```text
### YYYY-MM-DD HH:MM | <model-id> | <EVENT>
```

Recommended event types:

| Event | Use |
|---|---|
| `PROJECT_BOOTSTRAPPED` | First installation |
| `RE_ENGAGED` | Existing AIMemory found and reused |
| `PROMPT` | User request worth preserving |
| `WORK_START` | Start of a meaningful task |
| `WORK_END` | End of a meaningful task |
| `FILES_CREATED` | Files created |
| `FILES_MODIFIED` | Files modified |
| `FILES_MOVED` | Files moved |
| `FILES_DELETED` | Files deleted |
| `DECISION` | Durable project decision |
| `NOTE` | Assumption, risk, uncertainty, or context |
| `HANDOFF` | Work sent to another agent |
| `HANDOFF_RECEIVED` | Work received from another agent |
| `HANDOFF_CLOSED` | Handoff completed |
| `REVIEW_RESPONSE` | Review result from another agent |
| `CHECKPOINT` | Mid-task progress snapshot for continuity |
| `CONTEXT_PRESSURE` | Context is becoming heavy enough to risk continuity |
| `HANDOFF_RECOMMENDED` | Agent recommends switching to another agent or a fresh session |
| `BLOCKER` | Blocking issue |
| `CORRECTION` | Correction to an earlier event |

Keep log entries concise. If the content is long, create a separate markdown file under `AIMemory/` and link to it from `work.log`.

---

## 5. Safe append discipline

When appending to `work.log`, use one append operation per event.

POSIX shell example:

```bash
cat >> AIMemory/work.log <<'EOF'
### YYYY-MM-DD HH:MM | <model-id> | WORK_END
Status: complete
Summary: <summary>
EOF
```

If `flock` is available and multiple agents may write concurrently, use it.

```bash
flock AIMemory/work.log -c "cat >> AIMemory/work.log <<'EOF'
### YYYY-MM-DD HH:MM | <model-id> | WORK_END
Status: complete
Summary: <summary>
EOF"
```

Do not use read-modify-write editing for `work.log`.

---

## 6. Parallel work rule

Prefer sequential handoff:

```text
Agent A completes work
→ logs result
→ creates handoff
→ Agent B reads memory and continues
```

If multiple agents work at the same time, separate responsibilities clearly.

Example:

```text
Codex: game logic
Claude: UI structure
Gemini: UX text and explanation
OpenCode: refactoring and code quality
```

When parallel work ends, one agent should integrate the changes and resolve conflicts.

---

## 7. Handoff files

Use handoff files when one agent wants another agent to continue, review, or answer something.

Create handoff files under:

```text
AIMemory/handoffs/
```

Filename format:

```text
handoff_<topic>.<from-model-id>.md
```

Example:

```text
AIMemory/handoffs/handoff_auth-review.gpt-5-codex.md
```

Handoff file template:

```markdown
# Handoff: <topic>

From: <model-id>
From-vendor: <vendor>
To: <target-agent-or-model>
Date: <YYYY-MM-DD HH:MM>
Type: <REVIEW_REQUEST | REVIEW_RESPONSE | CONTINUATION | QUESTION | ANSWER | BLOCKER | STATUS_REPORT | PROPOSAL>
Priority: <LOW | NORMAL | HIGH | BLOCKING>
Required capability: <filesystem-read | filesystem-write | shell-exec | web-search | code-sandbox | other>

## Summary

<2-4 sentence summary>

## Context

<what led to this handoff>

## Current state

<where the work stands right now>

## Changed files

- `<path>`: <what changed>

## Decisions

- <decision>

## Open issues

- <issue>

## Requested action

- [ ] <specific action for receiving agent>

## Notes

<any constraints, assumptions, or warnings>
```

When creating a handoff, append a `HANDOFF` event to `work.log`.

When receiving one, append `HANDOFF_RECEIVED`.

When done, append `HANDOFF_CLOSED`.

---

## 8. Tiered memory

AIMemory uses three memory tiers.

| Tier | Location | Purpose | Read frequency |
|---|---|---|---|
| Hot | `work.log` | Recent task history | Every meaningful task |
| Warm | `archive/` | Older detailed logs | When relevant |
| Cold | `cold/` | Long-term summaries | Only when needed |

Do not load all old memory by default.

Use `INDEX.md` to find relevant old context.

---

## 9. Rotation guidance

If `work.log` becomes too long, move older entries into a dated archive file.

Suggested threshold:

```text
Keep the most recent 50 meaningful events in work.log.
Move older events to archive/work-YYYY-MM-DD.log.
Update INDEX.md with topics and archive locations.
```

Do not delete old events. Move them.

If a monthly summary is created, place it under:

```text
AIMemory/cold/digest-YYYY-MM.md
```

After creating or updating a cold digest, update `PROJECT_OVERVIEW.md` if project-level facts changed.

---

## 10. Project overview maintenance

Update `AIMemory/PROJECT_OVERVIEW.md` when any of the following changes:

- project purpose
- technology stack
- core architecture
- locked-in decision
- current major task
- open handoff
- important constraint

Keep it short enough for a new agent to read quickly.

---

## 11. Index maintenance

Update `AIMemory/INDEX.md` when creating:

- new archive files
- new cold digests
- handoff files
- durable topic notes
- major design or review documents

The index should help future agents find relevant memory without reading everything.

---

## 12. Markdown artifact rule

Unless a file is a harness-required root instruction file, new AI-authored markdown should live under `AIMemory/`.

Examples:

```text
AIMemory/refactor-plan.<model-id>.md
AIMemory/review-auth.<model-id>.md
AIMemory/handoffs/handoff_ui-review.<model-id>.md
```

---

## 13. Checkpoint and context-pressure protocol

Agents must actively protect project continuity during long tasks.

### 13.1 Automatic checkpoint triggers

Create a `CHECKPOINT` event in `AIMemory/work.log` when any of the following is true:

- The task has modified 3 or more files.
- The task has created, moved, or deleted project files.
- The task has multiple phases such as implementation, testing, refactoring, and documentation.
- The agent has inspected many files and the working context is becoming large.
- The user changes direction during the task.
- A meaningful partial result exists and losing it would make continuation difficult.
- The agent suspects the current session may become too long to continue safely.
- The agent is about to run a risky change, broad refactor, or large replacement.

A checkpoint should be concise and must include:

```text
### YYYY-MM-DD HH:MM | <model-id> | CHECKPOINT
Summary: <what has been done>
Changed files: <files changed so far>
Current focus: <what the agent is doing now>
Remaining: <what still needs to be done>
Risks: <context pressure, uncertainty, conflicts, missing tests, or blockers>
```

Do not ask the user before writing an obviously useful checkpoint. Checkpoints are normal continuity maintenance.

### 13.2 When to ask the user

If the agent is unsure whether to continue, summarize, or hand off, ask the user briefly.

Use this style:

```text
The task is getting large. I can continue, but it would be safer to write a checkpoint to AIMemory first.
```

Do not ask repeatedly. If a checkpoint is clearly useful, write it without asking.

### 13.3 Context-pressure detection

Agents usually cannot know the exact remaining token budget. Therefore, use context-pressure signals instead of pretending to know exact token usage.

Treat the session as context-heavy when two or more of the following are true:

- The conversation is long.
- Many files have been read.
- Several files have been modified.
- The task has changed direction more than once.
- The user has asked for full source code, full-document rewrites, or large generated artifacts.
- The agent is carrying many unresolved assumptions.
- The next step requires careful continuity.
- The agent notices increasing risk of forgetting earlier constraints.
- The agent is summarizing or re-reading earlier context repeatedly.

When context pressure is high, append a `CONTEXT_PRESSURE` event and consider writing a handoff file.

```text
### YYYY-MM-DD HH:MM | <model-id> | CONTEXT_PRESSURE
Reason: <why the current context is becoming risky>
Current state: <short state summary>
Recommendation: <continue | checkpoint | handoff | fresh session>
```

### 13.4 Handoff recommendation

Recommend handoff or fresh-session continuation when:

- The current agent is near its practical context limit.
- The task has a stable partial result but more work remains.
- Another agent is better suited for the next phase.
- The user has mentioned switching tools or agents.
- The remaining work is separable, such as review, test writing, UX copy, documentation, or refactoring.
- A new session would reduce the risk of losing earlier constraints.

When recommending a handoff, append `HANDOFF_RECOMMENDED` to `work.log`.

```text
### YYYY-MM-DD HH:MM | <model-id> | HANDOFF_RECOMMENDED
Reason: <context pressure | phase transition | specialist review | user request>
Suggested target: <agent/model if known, otherwise unknown>
Recommended next step: <specific next action>
```

User-facing wording should be short and practical:

```text
I have recorded the current state in AIMemory. This is a good point to continue in another agent or a fresh session.
```

### 13.5 Handoff preparation

When preparing for another agent, create a file under:

```text
AIMemory/handoffs/
```

Use this event in `work.log`:

```text
### YYYY-MM-DD HH:MM | <model-id> | HANDOFF
Summary: <what was completed>
Target: <target agent if known>
Handoff file: AIMemory/handoffs/handoff_<topic>.<model-id>.md
Reason: <context pressure | specialist review | user request | phase transition>
```

The handoff file must include:

- task summary
- changed files
- decisions made
- current state
- remaining work
- known risks
- exact next recommended action

### 13.6 Minimum continuity rule

Before stopping a large task, switching agents, or handing control back after partial progress, ensure at least one of the following exists:

- a recent `CHECKPOINT` event
- a `WORK_END` event with clear remaining steps
- a `HANDOFF` event and handoff file
- a `CONTEXT_PRESSURE` event explaining why continuation should move elsewhere

---

## 14. Sensitive information

Do not put secrets into AIMemory.

Do not record:

- API keys
- passwords
- private tokens
- session cookies
- personal information not needed for project continuity

If the project is public, recommend adding `AIMemory/` to `.gitignore` unless the user explicitly wants to version it.

---

## 15. When uncertain

If unsure what to do, append a concise `NOTE` to `work.log` explaining:

- what is uncertain
- what you assumed
- what the next agent should verify

Then proceed conservatively.
```

---

Now perform the setup tasks.
