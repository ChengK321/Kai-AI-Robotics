# LeRobot Round 01 — Main Pipeline

## Objective

Within one week, understand and reproduce the minimum LeRobot learning pipeline:

```text
Dataset → Policy → Train → Checkpoint → Eval
```

Use **ACT** as the first policy because it is small enough for an RTX 3070 laptop and structurally simple enough to expose LeRobot's abstractions.

## Why ACT first

ACT is ideal for source-code training because it forces us to understand:

- robot observations vs actions;
- temporal action chunks;
- dataset time indexing;
- input/output feature schemas;
- preprocessing and normalization;
- policy construction;
- offline imitation-learning training;
- checkpointing and evaluation.

The goal is not to become an ACT specialist. ACT is the probe we use to understand LeRobot.

---

## Source map for Round 01

Read only these files first.

### Entry / orchestration

- `src/lerobot/scripts/lerobot_train.py`
  - main training orchestration;
  - dataset creation;
  - policy creation;
  - pre/post processors;
  - dataloader;
  - optimizer;
  - train/eval/checkpoint loop.

### Dataset

- `src/lerobot/datasets/lerobot_dataset.py`
  - `LeRobotDataset`;
  - frame/episode access;
  - temporal samples.

- `src/lerobot/datasets/dataset_metadata.py`
  - `LeRobotDatasetMetadata`;
  - `info.json`, `stats.json`, tasks/episodes metadata.

- `examples/dataset/load_lerobot_dataset.py`
  - simplest concrete dataset usage.

### Policy abstraction

- `src/lerobot/policies/factory.py`
  - `make_policy`;
  - policy configuration lookup;
  - pre/post processor creation;
  - how a policy backend plugs into the common training stack.

### ACT

- `src/lerobot/policies/act/configuration_act.py`
  - input/output features;
  - `chunk_size`;
  - `n_action_steps`;
  - vision backbone;
  - transformer/VAE hyperparameters.

- `src/lerobot/policies/act/modeling_act.py`
  - read only after the simple training example works.

- `examples/tutorial/act/act_training_example.py`
  - **the first executable example**;
  - shows metadata → features → ACTConfig → ACTPolicy → processor → dataset → dataloader → loss → optimizer.

### Evaluation

- `src/lerobot/scripts/lerobot_eval.py`
  - only inspect after training works.

---

# Day 1 — Repository orientation + environment

## Goal

Know where the main abstractions live and get a source checkout running.

## Tasks

1. Clone upstream separately from this lab repo:

```bash
git clone https://github.com/huggingface/lerobot.git
cd lerobot
```

2. Create an isolated environment. Prefer Python 3.11 for compatibility unless current upstream installation docs explicitly require another version.

3. Install from source with training dependencies according to current official installation docs.

4. Verify:

```bash
python -c "import torch; print(torch.__version__); print(torch.cuda.is_available()); print(torch.cuda.get_device_name(0) if torch.cuda.is_available() else 'cpu')"
python -c "import lerobot; print(lerobot.__version__)"
lerobot-info
```

5. Browse only these directories:

```text
src/lerobot/datasets/
src/lerobot/policies/
src/lerobot/scripts/
src/lerobot/robots/
src/lerobot/envs/
examples/tutorial/act/
```

## Output

Create `notes/day-01-repo-map.md` containing:

- five major LeRobot subsystems;
- one sentence describing each subsystem;
- a hand-written call-flow diagram;
- environment versions and GPU information.

## Pass condition

Without searching the repo, answer:

1. Where is the training entry point?
2. Where is dataset metadata represented?
3. Where does a policy backend register itself?
4. Where is ACT configured?
5. Where will evaluation enter the system?

---

# Day 2 — Understand LeRobotDataset

## Goal

Understand what one robot-learning sample actually contains.

## Tasks

1. Read:
   - `examples/dataset/load_lerobot_dataset.py`;
   - `src/lerobot/datasets/lerobot_dataset.py` around `LeRobotDataset`;
   - `src/lerobot/datasets/dataset_metadata.py` around `LeRobotDatasetMetadata`.

2. Load a small official dataset.

3. Print:
   - dataset length;
   - fps;
   - features;
   - one sample's keys;
   - state shape;
   - action shape;
   - image shape if available;
   - episode index / frame index / timestamp.

4. Explain the difference among:
   - frame;
   - episode;
   - observation;
   - action;
   - task;
   - delta timestamp.

5. Draw the physical meaning of one training sample.

## Mini experiment

Compare:

```text
current observation → current action
```

with ACT's temporal target:

```text
current observation → [a_t, a_t+1, ..., a_t+chunk-1]
```

## Output

Create:

- `notes/day-02-dataset.md`
- `experiments/inspect_dataset.py`

Do not copy LeRobot internals. Write a small external inspection script using public APIs.

## Pass condition

You can explain why robot-learning datasets are not ordinary image-classification datasets and why time alignment matters.

---

# Day 3 — Run the minimal ACT training example

## Goal

Complete one genuine forward/backward/update step on the RTX 3070.

## Tasks

1. Read `examples/tutorial/act/act_training_example.py` line by line.

2. Before running it, predict the data flow:

```text
metadata
  ↓
feature schema
  ↓
ACTConfig
  ↓
ACTPolicy
  ↓
delta_timestamps
  ↓
LeRobotDataset
  ↓
DataLoader
  ↓
preprocessor
  ↓
policy.forward
  ↓
loss.backward
  ↓
optimizer.step
```

3. Copy the *idea*, not the upstream file, into a small lab script and remove Hub upload calls.

4. Set device to CUDA.

5. Start with one training step.

6. Print:
   - input keys/shapes after preprocessing;
   - loss;
   - peak CUDA memory;
   - elapsed time.

## Output

Create:

- `experiments/minimal_act_train.py`
- `notes/day-03-act-minimal-training.md`

## Pass condition

A checkpoint is saved locally and you can point to the exact line where model parameters change.

---

# Day 4 — Trace ACT configuration and model boundary

## Goal

Understand ACT without trying to memorize every transformer implementation detail.

## Tasks

1. Read `configuration_act.py` completely.

2. Locate in `modeling_act.py`:
   - policy class;
   - model construction;
   - image encoder;
   - state input;
   - action-chunk output;
   - training loss;
   - `select_action` / inference path.

3. Answer:
   - Why is `chunk_size` different from `n_action_steps`?
   - Why are action delta indices required?
   - What does the VAE contribute during ACT training?
   - Which inputs depend on the robot embodiment?
   - Which architecture components are robot-independent?

## Required diagram

Draw:

```text
camera images ─→ ResNet ─┐
                         ├→ Transformer → action chunk
robot state ─────────────┘
                  ↑
          latent / VAE (training)
```

Then annotate actual tensor shapes from your dataset.

## Output

- `notes/day-04-act-architecture.md`

## Pass condition

You can change `chunk_size` and predict all downstream data-shape consequences before running the code.

---

# Day 5 — Read the real `lerobot-train` pipeline

## Goal

Move from tutorial code to production-style LeRobot architecture.

## Tasks

Read `src/lerobot/scripts/lerobot_train.py`, but only follow these objects/functions initially:

```text
TrainPipelineConfig
    ↓
make_train_eval_datasets
    ↓
make_policy
    ↓
make_pre_post_processors
    ↓
make_optimizer_and_scheduler
    ↓
make_dataloaders
    ↓
update_policy
    ↓
checkpoint / eval
```

Ignore distributed/FSDP/remote-job details on the first pass.

## Key insight to extract

The tutorial manually instantiates `ACTPolicy`.

The full trainer uses a factory-based design:

```text
CLI config
   ↓
policy.type = act / diffusion / pi0 / groot / ...
   ↓
make_policy(...)
   ↓
common trainer
```

This is one of the most important architectural ideas in LeRobot.

## Output

Create:

- `notes/day-05-training-call-chain.md`

Include two diagrams:

1. tutorial training flow;
2. full CLI training flow.

Then explain what LeRobot gains from the factory abstraction.

## Pass condition

You can answer: "If I implement a new policy, which parts of `lerobot_train.py` ideally should not change?"

---

# Day 6 — First meaningful modification / ablation

## Goal

Stop being a user of the repository and begin behaving like an engineer studying it.

Run a small controlled experiment. Pick **one** initially:

### Option A — Action chunk length

Compare small settings such as:

```text
chunk_size = 10
chunk_size = 30
chunk_size = 50
```

Measure:

- GPU memory;
- step time;
- training loss behavior.

### Option B — Vision backbone initialization

Compare pretrained vs non-pretrained backbone for a short smoke test.

### Option C — Dataset size

Train on controlled subsets and inspect optimization behavior.

Do not claim task-success conclusions from tiny training runs. This exercise is about understanding system effects.

## Output

- `experiments/round-01-ablation/`
- `notes/day-06-ablation.md`

Include a small result table and interpretation.

## Pass condition

You make a prediction before the experiment, record the result, and explain discrepancies.

---

# Day 7 — Eval + architecture summary

## Goal

Close the loop and produce a reusable mental model.

## Tasks

1. Inspect `lerobot_eval.py` and the current official evaluation docs.
2. Understand the difference between:
   - offline training loss;
   - held-out dataset loss;
   - closed-loop environment success rate;
   - real-robot task success rate.
3. If a lightweight simulation evaluation is practical on the local machine, run it. If not, trace the path and postpone compute-heavy execution.
4. Write the final architecture summary.

## Final output

Create:

- `architecture.md`
- `round-01-retrospective.md`

The architecture document must include:

```text
Robot / Dataset
      ↓
LeRobotDataset + Metadata
      ↓
Feature schema / temporal sampling
      ↓
Preprocessor
      ↓
Policy backend
      ↓
Loss / optimizer
      ↓
Checkpoint
      ↓
Inference / Env / Robot
      ↓
Evaluation
```

and identify the extension points for:

- new robot;
- new dataset;
- new policy;
- new environment;
- new evaluator.

---

# Round 01 Gate

Do **not** proceed to Diffusion/VLA until all five are true:

- [ ] I can inspect a LeRobot dataset and explain temporal action targets.
- [ ] I have run ACT forward/backward/update on my RTX 3070.
- [ ] I understand the ACT input/output feature boundary and action chunking.
- [ ] I can trace `lerobot-train` from config to dataset to policy to optimizer.
- [ ] I have completed at least one controlled modification/ablation and recorded the result.

## Deliverables expected in this lab repository

```text
upstream-study/lerobot/
├── README.md
├── round-01-main-pipeline.md
├── architecture.md                    # Day 7
├── round-01-retrospective.md          # Day 7
├── notes/
│   ├── day-01-repo-map.md
│   ├── day-02-dataset.md
│   ├── day-03-act-minimal-training.md
│   ├── day-04-act-architecture.md
│   ├── day-05-training-call-chain.md
│   └── day-06-ablation.md
└── experiments/
    ├── inspect_dataset.py
    ├── minimal_act_train.py
    └── round-01-ablation/
```

We create these files only when the corresponding work is actually completed; no fake placeholders for learning results.
