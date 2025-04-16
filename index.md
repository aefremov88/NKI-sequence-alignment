---
layout: home
---
## CMU 15-618 Final Project: Sequence Alignment Using NKI

Permalink: [https://aefremov88.github.io/NKI-sequence-alignment/](https://aefremov88.github.io/NKI-sequence-alignment/)

## Team members
Kandasamy Chokkalingam <kchokkal@andrew.cmu.edu>

Anton Efremov <aefremov@andrew.cmu.edu>

## Summary
In this document, we propose implementing the **Smith–Waterman algorithm** for local sequence alignment using AWS's Neuron Kernel Interface to program on their systolic-array based NeuronCores. This algorithm features a 2D data passing pattern, which presents a strong opportunity for multiple levels of parallelization on their combined vectorized, SPMD, and systolic array based system.

## Table of Contents
- [Proposal](/NKI-sequence-alignment/2025/03/26/proposal.html)
- [Milestone Report](/NKI-sequence-alignment/2025/04/15/milestone.html)

## Up-to-date Schedule

✅ indicates the task is done, ☑️ indicates the task is in progress, ✔️ indicates the task is planned.

{% capture table %}
| Task                                                                       | Week 1 | Week 2 | Week 3 (04/15~04/18) | Week 3.5 (04/19~04/22) | Week 4 (04/22~04/25) | Week 4.5 (04/25~04/28) |
|----------------------------------------------------------------------------|--------|--------|----------------------|------------------------|----------------------|------------------------|
| Set up Trainium Environment                                                | ✅      |        |                      |                        |                      |                        |
| Start running NKI examples                                                 | ✅      |        |                      |                        |                      |                        |
| Write NKI primitives to use in algorithm                                   | ✅      |        |                      |                        |                      |                        |
| Implement simple SW algorithm in NKI                                       |        | ☑️      |                      |                        |                      |                        |
| Profile algorithm to determine optimizations                               |        | ☑️      |                      |                        |                      |                        |
| Optimization: Convert to operations to NKI.isa for efficiency              |        |        | ✔️ (Kandasamy)        |                        |                      |                        |
| Optimization: Improve memory access pattern                                |        |        | ✔️ (Anton)            |                        |                      |                        |
| Profiling: Start running and profile real workloads                        |        |        | ✔️ (Combined)         |                        |                      |                        |
| Profiling: Pipeline computation with tiling mentioned in CUDASW 4.0        |        |        |                      | ✔️ (Anton)              |                      |                        |
| Experiment with engine configurations to find better patterns              |        |        |                      | ✔️ (Kandasamy)          |                      |                        |
| Profiling: Analyze and optimize performance for 4x4 grid chip              |        |        |                      |                        | ✔️ (Combined)         |                        |
| Rerun profiling experiments to identify algorithmic bottlenecks            |        |        |                      |                        | ✔️ (Anton)            |                        |
| Run different type of real workloads to identify poor automatic pipelining |        |        |                      |                        | ✔️ (Kandasamy)        |                        |
| [EXTRA] Compare with CUDA library for workload & data movement comparison  |        |        |                      |                        |                      | ✔️ (Anton)              |
| [EXTRA] Implement multiple sequence alignment                              |        |        |                      |                        |                      | ✔️ (Kandasamy)          |
{% endcapture %}

{% include fancy-tables.liquid markdown=table %}
