# Kai AI Robotics

> Personal engineering lab for the transition from classical robot planning/control to **AI Robotics / Embodied AI Systems Engineering**.

This repository is not a course-note dump. Its purpose is to build reusable engineering assets through three parallel tracks:

1. **Foundations** — only the AI/ML fundamentals needed to build and debug robot intelligence systems.
2. **Open-source study & reproduction** — read, run, modify, and benchmark authoritative repositories/papers.
3. **Systems** — integrate VLM/LLM/Robot Learning with ROS2, navigation, recovery, evaluation, and deployment.

## Target role

**Embodied AI Systems / Robot Intelligence Engineer**

Core capability target:

```text
Natural-language task
        ↓
VLM / Embodied Reasoner
        ↓
Task Planner / Behavior Tree
        ↓
Perception + Navigation + Learned Skills
        ↓
Classical Planner / Policy / Controller
        ↓
Real or simulated robot
        ↓
Evaluation → Failure Mining → Data → Retraining
```

## Repository structure

```text
Kai-AI-Robotics/
├── foundations/            # PyTorch / Transformer / VLM essentials
├── upstream-study/         # Source-code study of major open-source projects
├── paper-reproductions/    # Reproduction + ablation + migration experiments
├── systems/                # End-to-end AI + Robot system projects
├── benchmarks/             # Unified evaluation protocols and baselines
├── experiments/            # Small controlled experiments and logs
├── docs/                   # Roadmap, architecture notes, learning records
├── scripts/                # Reusable setup / training / evaluation scripts
└── README.md
```

## Current focus order

### System layer first
- Nav2 / Behavior Tree
- VLM semantic navigation
- LLM/embodied task planning
- Recovery and success/failure detection

### Robot-learning layer
- LeRobot
- Behavior Cloning / ACT
- Diffusion Policy
- VLA fine-tuning and evaluation

### Frontier tracking
- NVIDIA Isaac GR00T
- Physical Intelligence openpi
- Hy-Embodied / other strong open VLA stacks

The rule is: **understand the problem first, then the model; reproduce the result, then modify it; finally integrate it into our own robot system.**

## Learning loop

Every substantial topic should pass four levels:

1. **Run** — reproduce the official example or benchmark.
2. **Understand** — draw the data/model/runtime call graph and identify key source files.
3. **Modify** — change at least one meaningful component or experimental variable.
4. **Integrate** — move the learned component into one of our own systems and evaluate failure modes.

A repository is not considered "learned" after `git clone && python demo.py`.

## 12-month output target

By the end of the first year this repository should contain three portfolio-grade systems:

- **Embodied Semantic Navigation Agent** — VLM + task planning + Behavior Tree + Nav2 + recovery.
- **Robot Learning Runtime** — dataset → training → policy deployment, initially ACT/Diffusion/VLA.
- **Robot Evaluation & Failure Engine** — automatic evaluation, failure classification, hard-case mining and regression testing.

Secondary project: **Robot Debug Copilot**, using robot logs/video/state to assist failure diagnosis.

## Hardware strategy

- Early stage: use the existing RTX 3070 laptop for development, ROS2, VLM inference, and small policy training.
- Medium stage: rent 24 GB cloud GPUs only when experiments exceed local VRAM.
- Large VLA reproduction: temporarily use 48–80 GB cloud GPUs when necessary.
- Real robot hardware is intentionally delayed until the software/learning pipeline passes the corresponding gate.

## Engineering principles

- Project > course.
- Failure analysis > successful demo video.
- Reproducible experiment > vague impression.
- Stable concepts > chasing every new model.
- AI and classical robotics should be combined according to system requirements, not ideology.
- Never use proprietary employer code, datasets, logs, or confidential design details in this public repository.

## Status

Initial repository scaffold. Content will be filled progressively along the 12-month engineering roadmap.
