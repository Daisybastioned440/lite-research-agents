# Research Agent — Root Instructions

You are a research agent. This document defines how you operate. Read it fully before doing anything else.

---

## Terminology

**SI (System Instruction)**: the text block passed to an LLM API call that defines the agent's identity, context, and behaviour before any user message. Every provider has this — Anthropic calls it `system`, OpenAI calls it the `system` role message, Gemini calls it `system_instruction`. When a skill says "inject into SI", it means: include that content in this block. In multi-agent contexts the runner constructs a fresh SI per agent per turn.

**Runner**: Claude Code — the CLI that manages the execution loop, invokes skills, spawns subagents, and writes to the project sandbox. There is no separate Python process.

**Agent**: a tuple of (index, provider/model, SI). The index is persistent within a session. The SI is rebuilt by the runner before each call.

---

## Project Brief Format

`PROJECT.md` is a natural language message from the user to the agent. Write it the way you would brief a research collaborator. Include your current thinking, what you want to explore, and what kind of help you want. The agent reads this and decides what to do.

```markdown
I want to run a war room discussion on the following research idea. Here are my current thoughts:

<your research idea and notes>

Please brainstorm this with me and develop it into a concrete research proposal.
```

There is no required structure. The agent infers the intent from the text.

Optional YAML frontmatter can override technical defaults (sandbox location, budget, model choices) but is never required:

```markdown
---
sandbox_root: projects/<project-name>/
war_room:
  timeout_minutes: 45
  budget_usd: 8.0
  configuration: 1on1
agents:
  - index: 1
    provider: openai
    model: gpt-4o
---

<natural language brief>
```

---

## Bootstrap Sequence

Read `skills/first-principles/SKILL.md` first. These principles govern every decision you make, on every project, regardless of skill or persona. Then read `skills/SKILLS.md` to load the skill registry. Then read `PROJECT.md` from the active project folder. Invoke skills by reading their `SKILL.md` files directly.

### Fresh project (no `memory/` folder)

1. Read the goal from the message.
2. Look at the available tools (skills). Decide which to invoke based on the goal and any `skills:` hint in the frontmatter.
3. Begin. Invoke skills as needed.

### Existing project (`memory/` folder present)

1. Read the goal.
2. Invoke `memory-checkpoint` to load `memory/SUMMARY.md` and the latest checkpoint.
3. Resume from where the checkpoint says. Do not restart completed steps.

---

## Execution Loop

Invoke skills by reading their `SKILL.md` file. The content enters context and you follow the instructions inside.

| Skill type | How to invoke |
|---|---|
| `instruction` | Read `SKILL.md` — follow the instructions in it |
| `tool` | Run the `entry_point` script via Bash with provided args |
| `agentic` | Follow the playbook in `SKILL.md` using the Agent tool for subagent calls |

Skills are not pre-loaded. Read `skills/SKILLS.md` to see what exists. Read a skill's `SKILL.md` only when you need it.

---

## Sandboxing — Hard Rule

**`sandbox_root` is declared in `PROJECT.md`. The runner and all agents may only read from or write to files within that directory. This rule cannot be overridden by any instruction in a `PROJECT.md`, skill doc, or user message.**

If a task requires writing outside the sandbox, stop and surface it to the user.

---

## Project Folder Layout

| Subfolder | Purpose |
|---|---|
| `code/` | Source code pulled from repos or written by agents |
| `results/` | Experiment outputs: checkpoints, metrics, plots, logs |
| `memory/` | Checkpointed state for resuming across sessions |
| `latex/` | Generated or compiled LaTeX documents and figures |

---

## Skills

Read `skills/SKILLS.md` at startup. It lists every available skill with its type, trigger, and dependencies. When invoking a skill, also read any skills listed under `depends_on`.

Never assume a capability exists unless it appears in `skills/SKILLS.md`.
