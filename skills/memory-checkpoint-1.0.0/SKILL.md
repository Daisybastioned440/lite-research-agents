---
name: memory-checkpoint
description: Save and restore agent working state across sessions. Use after every major step, before any risky operation, and whenever a session is ending. On resume, use this skill to reconstruct prior state before taking any action.
---

# Memory Checkpoint

This skill defines how agents write and read checkpoints in `memory/checkpoints/`. Checkpoints are the sole mechanism for persisting state across sessions.

---

## When to Checkpoint

**Write a checkpoint:**
- After any task or sub-task from `PROJECT.md` completes
- Before any destructive or long-running operation (training run, repo clone, file deletion)
- When a session is ending (voluntary or due to context limits)
- When a significant finding is made that future agents must know about
- When a blocker is hit that cannot be resolved immediately

**Read a checkpoint:**
- At the start of every session against an existing project (mandatory — see bootstrap sequence in `AGENT.md`)
- When you need deeper context about a specific past step

---

## Writing a Checkpoint

### Step 1 — Determine the timestamp

Generate the current timestamp in `YYYYMMDD_HHMMSS` format (UTC). This becomes the checkpoint folder name.

Example: `20260328_142301`

### Step 2 — Create the checkpoint folder

Create the directory:
```
<sandbox_root>/memory/checkpoints/<timestamp>/
```

All checkpoint files go inside this folder.

### Step 3 — Write `manifest.md`

```markdown
# Checkpoint: <timestamp>

- **Trigger**: <one of: task_complete | session_end | pre_risky_op | finding | blocker | manual>
- **Step completed**: <exact task name from PROJECT.md, or "none" if session start>
- **Next step**: <exact task name or description of what should happen next>
- **Session notes**: <anything unusual, errors hit, decisions made, shortcuts taken>
```

Keep this file under 300 words. It is the fast-triage layer — agents read this before deciding whether to load full agent state.

### Step 4 — Write `agents.json`

```json
{
  "checkpoint": "<timestamp>",
  "trigger": "<trigger>",
  "step": "<step completed>",
  "agents": [
    {
      "index": 0,
      "role": "<orchestrator | executor | analyst | writer | ...>",
      "provider": "<anthropic | openai | google | ...>",
      "model": "<model id>",
      "status": "<active | idle | blocked | done>",
      "state_file": "agent-0.md"
    }
  ]
}
```

Include one entry per agent that was active during this session. If only one agent ran, include only index 0.

### Step 5 — Write `agent-N.md` for each agent

One file per agent, named `agent-<index>.md`. Use this exact structure:

```markdown
# Agent <index> State — <timestamp>

- **Role**: <role>
- **Model**: <provider>/<model>

## Completed This Session
<bulleted list of what this agent did since the last checkpoint>

## Key Outputs
<bulleted list of file paths written, values computed, or findings made — be specific>
| Path | Description |
|---|---|
| `results/checkpoints/ckpt-500.pt` | Training checkpoint at iteration 500 |

## Working Assumptions
<any assumptions this agent made that future agents should know about>

## Environment State
<installed packages, env vars set, processes running, ports open — anything not captured in files>

## Blockers / Issues
<anything that went wrong or is unresolved — "none" if clean>

## Next Action
<the exact next thing this agent should do when resumed — be specific enough to act on>
```

### Step 6 — Update `memory/SUMMARY.md`

Rewrite (do not append) `SUMMARY.md` after every checkpoint. Keep it under 400 words.

```markdown
# Project Summary

- **Project**: <PROJECT.md title>
- **Last checkpoint**: `memory/checkpoints/<timestamp>/`
- **Overall status**: <one of: not_started | in_progress | blocked | complete>

## Tasks
| Task | Status |
|---|---|
| Task 1: Pull Repository | complete |
| Task 2: Environment Setup | complete |
| Task 3: Launch Training | in_progress |
| Task 4: Checkpoint Management | not_started |
| Task 5: Plot Training Loss | not_started |

## Critical Facts
<bullet list of must-know facts: key file paths, important values, decisions that affect future steps>
- Training output dir: `results/checkpoints/`
- Dataset: shakespeare_char (~1M tokens)
- GPU not available; running on CPU, ~2h estimated

## Resume From
<exact instruction for the next agent: which task, which step, any preconditions>
```

---

## Reading a Checkpoint (Resume Sequence)

1. Read `memory/SUMMARY.md` — always, unconditionally. This tells you overall status and points to the latest checkpoint.
2. Read `memory/checkpoints/<latest>/manifest.md` — fast triage. Confirms what was last done and what is next.
3. Read `memory/checkpoints/<latest>/agents.json` — understand who was running and their roles.
4. Load `agent-N.md` files only for agents whose roles are relevant to the next step.
5. Do not load older checkpoints unless you have a specific reason (e.g., debugging a past decision).

**Never skip step 1–3.** Even if you think you know the project state, always confirm from the checkpoint before acting.

---

## Checkpoint Retention

Do not delete old checkpoints automatically. Retention policy is set by the user in `PROJECT.md` under an optional `checkpoint_retention` field. If not specified, keep all checkpoints.

---

## Example Checkpoint Layout

```
memory/
├── SUMMARY.md
└── checkpoints/
    ├── 20260328_090000/          # session 1: pulled repo, set up env
    │   ├── manifest.md
    │   ├── agents.json
    │   └── agent-0.md
    ├── 20260328_142301/          # session 2: launched training, hit CPU warning
    │   ├── manifest.md
    │   ├── agents.json
    │   └── agent-0.md
    └── 20260328_154732/          # session 3: training complete, 2 agents
        ├── manifest.md
        ├── agents.json
        ├── agent-0.md
        └── agent-1.md
```
