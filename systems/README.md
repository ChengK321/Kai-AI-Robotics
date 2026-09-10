# Systems

Portfolio-grade end-to-end AI + Robot systems. This is the most important folder in the repository.

Planned systems:

```text
systems/
├── embodied-semantic-navigation/
├── robot-learning-runtime/
├── robot-evaluation-failure-engine/
├── robot-debug-copilot/
└── README.md
```

## 1. Embodied Semantic Navigation

Target architecture:

```text
Instruction
   ↓
VLM / Embodied Reasoner
   ↓
Task Planner / Behavior Tree
   ↓
Perception + Nav2 + Memory
   ↓
Planner / Controller
   ↓
Success Detection / Recovery
```

## 2. Robot Learning Runtime

Target loop:

```text
Dataset → Train → Checkpoint → Deploy → Evaluate
```

Start with ACT, then compare Diffusion Policy and one VLA backend.

## 3. Robot Evaluation & Failure Engine

Target loop:

```text
Episodes → Metrics → Failure Classification → Hard-case Mining → Retraining
```

## 4. Robot Debug Copilot

Use logs/video/state traces to produce timelines, failure hypotheses and debug suggestions.

Every system must include architecture, setup, reproducible demo, metrics, known failures, and next-step backlog.
