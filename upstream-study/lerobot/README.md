# LeRobot Study

Focus: dataset schema, robot abstraction, policy/trainer flow, evaluation, deployment, and how ACT/VLA backends plug into the runtime.

## Study method

Every round follows four levels:

1. **Run** — reproduce the official minimal workflow.
2. **Understand** — trace the main call chain and draw the architecture/data flow.
3. **Modify** — change a meaningful configuration or component and observe the effect.
4. **Integrate** — move reusable ideas/code into our own Robot Learning Runtime.

## Active round

- [Round 01 — Dataset → ACT → Train → Eval main pipeline](./round-01-main-pipeline.md)

## What we are deliberately NOT doing in Round 01

- No SO-101 hardware.
- No Pi0 / Pi0.5 / GR00T / large VLA training.
- No attempt to read the whole repository.
- No copying large chunks of upstream code into this repository.

The first goal is to understand LeRobot as an engineering system, not to collect model names.

## Round 01 graduation standard

Round 01 is complete only when we can explain and demonstrate this chain without relying on the README:

```text
LeRobotDataset / metadata
        ↓
feature definition + temporal sampling
        ↓
ACTConfig
        ↓
ACTPolicy
        ↓
preprocessor
        ↓
DataLoader
        ↓
policy(batch) → loss
        ↓
backward / optimizer
        ↓
checkpoint
        ↓
evaluation / inference
```

Status: **Round 01 active**.
