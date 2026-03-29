# open-research-agents

![War Room Demo](demo.png)

A lightweight interface for doing research with agents. Currently supports brainstorming ideas with multiple agents in a war room setting to produce a single research proposal. Future work will add experiment agents that carry out proposed experiments, and a master agent that iterates until results satisfy a set of reviewers.

## Install

```bash
uv sync
```

## Research Proposal

```bash
claude "Run the war room on projects/reward-model-interpretability/"
```

Monitor in a second terminal:

```bash
python skills/war-room/monitor.py --project projects/reward-model-interpretability/
```

## Experiment

## Review
