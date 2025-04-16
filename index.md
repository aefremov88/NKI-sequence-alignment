---
title: "15-618 Final Project: Sequence Alignment Using NKI"
layout: home
---

Permalink: [https://aefremov88.github.io/NKI-sequence-alignment/README.html](https://aefremov88.github.io/NKI-sequence-alignment/README.html)

## Team members
Kandasamy Chokkalingam <kchokkal@andrew.cmu.edu>

Anton Efremov <aefremov@andrew.cmu.edu>

## Summary
In this document, we propose implementing the **Smith–Waterman algorithm** for local sequence alignment using AWS's Neuron Kernel Interface to program on their systolic-array based NeuronCores. This algorithm features a 2D data passing pattern, which presents a strong opportunity for multiple levels of parallelization on their combined vectorized, SPMD, and systolic array based system.

*Table of Contents*
- [Proposal](/NKI-sequence-alignment/proposal.html)

## Schedule
{% capture table %}
| Task                                                                      | Week 1             | Week 2             | Week 3             | Week 4             |
|---------------------------------------------------------------------------|--------------------|--------------------|--------------------|--------------------|
| Set up Trainium Environment                                               | ✅ |                    |                    |                    |
| Start running NKI examples                                                | ✅ |                    |                    |                    |
| Write NKI primitives to use in algorithm                                  | ✅ |                    |                    |                    |
| Implement simple SW algorithm in NKI                                      |                    | ✅ |                    |                    |
| Profile algorithm to determine optimizations                              |                    | ✅ |                    |                    |
| Implement optimizations on SW algorithms                                  |                    |                    | ✅ |                    |
| Start running and profile real workloads                                  |                    |                    | ✅ |                    |
| Holistic analysis of varied real/worst-case workloads                     |                    |                    |                    | ✅ |
| [EXTRA] Compare with CUDA library for workload & data movement comparison |                    |                    |                    | ✅ |
| [EXTRA] Implement multiple sequence alignment                             |                    |                    |                    | ✅ |
{% endcapture %}

{% include fancy-tables.liquid markdown=table %}
