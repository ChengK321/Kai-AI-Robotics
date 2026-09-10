# Benchmarks

Unified evaluation protocols for comparing models and system versions.

Planned contents:

```text
benchmarks/
├── task-definitions/
├── metrics/
├── baselines/
├── regression/
└── README.md
```

Track at least:
- task success rate;
- completion time;
- intervention count;
- recovery success rate;
- inference latency;
- failure type distribution;
- generalization under controlled shifts.

The goal is to make new models replaceable backends that can be judged with the same evaluation suite.
