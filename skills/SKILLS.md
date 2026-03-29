# Skills Registry

Read this file at startup. Invoke a skill by reading its `SKILL.md` — the content enters context and you follow the instructions inside. For `type: tool` skills, run the `entry_point` script via Bash.

---

## Skill Types

| Type | How to invoke |
|---|---|
| `instruction` | Read `SKILL.md` — follow the instructions |
| `tool` | Run `entry_point` via Bash with provided args |
| `agentic` | Follow the playbook in `SKILL.md` using the Agent tool for subagent calls |

---

## Registry

### first-principles
```yaml
name: first-principles
type: instruction
description: Core research principles governing all project decisions.
trigger: Load at the start of every research project and whenever evaluating a research direction, method, or artifact.
path: skills/first-principles/SKILL.md
```

### memory-checkpoint
```yaml
name: memory-checkpoint
type: instruction
description: Defines how to save and load agent state as checkpoint files.
trigger: Invoke after each war-room round and before resuming any interrupted session. Also invoke whenever a long-running task could be lost if interrupted.
path: skills/memory-checkpoint/SKILL.md
```

### system-info
```yaml
name: system-info
type: instruction
description: Probe CPU, RAM, disk, GPU and produce experiment design recommendations.
trigger: Invoke when designing experiments that depend on hardware constraints, or when the user asks about what can be run locally.
path: skills/system-info/SKILL.md
```

### arxiv-watcher
```yaml
name: arxiv-watcher
type: tool
description: Search and summarize papers from ArXiv by topic, keyword, or paper ID.
trigger: Invoke when a claim needs literature support, when asked to find related work, or when a persona references a paper that should be verified.
path: skills/arxiv-watcher/SKILL.md
entry_point: skills/arxiv-watcher/scripts/search_arxiv.sh
args:
  - name: query
    type: string
    required: true
    description: Search query — keywords, topic, or arXiv paper ID
```

### persistent-persona
```yaml
name: persistent-persona
type: instruction
description: Mechanism for keeping an agent anchored to a caller-defined persona across sessions using private memo files and drift detection.
trigger: Invoke before every persona subagent call in war-room. Read the persona's private memo, check for drift, inject reset if needed.
path: skills/persistent-persona/SKILL.md
depends_on:
  - memory-checkpoint
```

### research-proposal
```yaml
name: research-proposal
type: instruction
description: Defines the structure and requirements for a 6-section research proposal.
trigger: Invoke when entering Phase 2 of war-room, or whenever a research proposal document needs to be produced.
path: skills/research-proposal/SKILL.md
```

### war-room
```yaml
name: war-room
type: agentic
description: >
  Multi-agent research session with ideation and proposal phases. Personas debate
  in sequential turns. Choose a configuration based on the task — 'full' (4 agents,
  deep ideation) or '1on1' (senior professor vs young faculty, fast focused debate).
trigger: Invoke when PROJECT.md asks for brainstorming, debate, or development of a research idea into a proposal.
path: skills/war-room/SKILL.md
depends_on:
  - first-principles
  - persistent-persona
  - memory-checkpoint
  - arxiv-watcher
  - research-proposal
```

---

## Adding a New Skill

1. Create `skills/<name>/SKILL.md` with `name`, `type`, `description`, and `trigger` in frontmatter.
2. For `tool` skills, add `entry_point` and `args`.
3. For `agentic` skills, write a full playbook loop in the body of `SKILL.md`.
4. Add a registry entry above in the same format.
