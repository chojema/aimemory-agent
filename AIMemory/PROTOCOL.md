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
| `GEMINI.md` | Gemini CLI |

These files should stay short. They should point to this protocol rather than duplicating it.

Do not place long project notes in root instruction files. Put durable notes under `AIMemory/`.

---

## 3. Start-of-task checklist

Before changing files, the active agent must:

1. Read `AIMemory/PROTOCOL.md`.
2. Read `AIMemory/PROJECT_OVERVIEW.md`.
3. Read the recent tail of `AIMemory/work.log`.
4. Check for an open or relevant handoff in `AIMemory/handoffs/` if the task involves continuation, review, or receiving another agent's work.
5. Check `AIMemory/INDEX.md` when older context may matter.

For trivial one-off answers that do not touch project files, use judgment. If the user explicitly says not to log the turn, do not log it.

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
