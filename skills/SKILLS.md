# Skills Index

Read this file first to discover what capabilities are available. Then load the full `SKILL.md` for each skill relevant to your current project before executing.

---

## How to Use Skills

1. Scan the registry below and identify relevant skills for the project goals.
2. Load each relevant `SKILL.md` in full — the index entry is just a summary.
3. Follow the skill's workflow exactly. Skills may call scripts, invoke APIs, or write files.
4. All file writes must still respect the project's `sandbox_root` (see `AGENT.md`).

---

## Skill Registry

| Skill | Path | When to Use |
|---|---|---|
| [First Principles](first-principles/SKILL.md) | `skills/first-principles/` | Core research principles governing all project decisions. Load for every research project. |
| [Memory Checkpoint](memory-checkpoint/SKILL.md) | `skills/memory-checkpoint/` | Saving and restoring agent working state between sessions. Use after every major step and before any risky operation. |
| [System Info](system-info/SKILL.md) | `skills/system-info/` | Probe CPU, RAM, disk, GPU/CUDA/MPS and produce experiment design recommendations. Run at the start of any compute-intensive project. |
| [ArXiv Watcher](arxiv-watcher/SKILL.md) | `skills/arxiv-watcher/` | Searching and summarizing academic papers from ArXiv by topic, keyword, or paper ID. |
| [Persistent Persona](persistent-persona/SKILL.md) | `skills/persistent-persona/` | Assign stable research personas to agents with biplate private memory for drift detection and self-correction. Use when running a war room session. |

---

## Adding New Skills

Each skill lives in its own folder: `skills/<slug>/`. It must contain a `SKILL.md` with YAML frontmatter (`name`, `description`). Register it in the table above before use.

Skills are model-agnostic. Any agent (Claude, GPT-4o, Gemini, etc.) can execute a skill by reading its `SKILL.md`.
