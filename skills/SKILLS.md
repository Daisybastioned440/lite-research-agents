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

| Skill | Version | Path | When to Use |
|---|---|---|---|
| [First Principles](first-principles-1.0.0/SKILL.md) | 1.0.0 | `skills/first-principles-1.0.0/` | Core research principles governing all project decisions. Load for every research project. |
| [ArXiv Watcher](arxiv-watcher-1.0.0/SKILL.md) | 1.0.0 | `skills/arxiv-watcher-1.0.0/` | Searching and summarizing academic papers from ArXiv by topic, keyword, or paper ID. |
| [Memory Checkpoint](memory-checkpoint-1.0.0/SKILL.md) | 1.0.0 | `skills/memory-checkpoint-1.0.0/` | Saving and restoring agent working state between sessions. Use at the end of every major step and before any potentially long or risky operation. |
| [System Info](system-info-1.0.0/SKILL.md) | 1.0.0 | `skills/system-info-1.0.0/` | Probe CPU, RAM, disk, GPU/CUDA/MPS and produce experiment design recommendations. Run at the start of any compute-intensive project. |

---

## Adding New Skills

Each skill must:
- Live in its own versioned folder: `skills/<slug>-<version>/`
- Contain a `SKILL.md` with YAML frontmatter (`name`, `description`)
- Contain a `_meta.json` with `slug`, `version`, `publishedAt`
- Be registered in the table above before it can be used

Skills are model-agnostic. Any agent (Claude, GPT-4o, Gemini, etc.) can execute a skill by reading its `SKILL.md`.
