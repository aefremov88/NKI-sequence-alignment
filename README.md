## Team members
Kandasamy Chokkalingam <kchokkal@andrew.cmu.edu>

Anton Efremov <aefremov@andrew.cmu.edu>

## Summary
In this document, we propose implementing the **Smith–Waterman algorithm** for local sequence alignment using the **systolic array parallel programming model** on AWS Trainium via the AWS Neuron Kernel Interface (NKI). This algorithm features a 2D data passing pattern, which presents a strong opportunity for parallelization on systolic array-based systems.

## Background
The Smith–Waterman algorithm solves the problem of local sequence alignment: given two sequences, it identifies the best matching pair of substrings by computing an optimal alignment score based on a user-defined scoring scheme. This includes a reward for character matches, penalties for mismatches, and gap penalties for insertions or deletions. It plays a foundational role in bioinformatics, identifying similar regions between DNA, RNA, or protein sequences. Due to its quadratic time complexity and the massive scale of modern sequencing datasets—often involving sequences with billions of elements—Smith–Waterman becomes a major computational bottleneck in genomic pipelines. There are GPU-accelerated libraries, such as CUDASW++, that take advantage of parallelism to achieve large speedups.

The algorithm's structure makes it well-suited for systolic arrays, as it involves computing a 2D dynamic programming matrix where each cell $H{i,j}$ depends only on its top, left, and top-left neighbors. As shown in the diagram, each cell is computed as the maximum of the match/mismatch score from the diagonal and possible insertion or deletion scores from the top or left, adjusted by gap penalties. This regular, local data dependency pattern aligns perfectly with the systolic array model, where processing elements (PEs) are arranged in a 2D grid and communicate only with neighbors in a rhythmic, pipelined fashion. Each PE can compute one cell and pass necessary values to adjacent PEs. This makes the Smith–Waterman algorithm an excellent candidate for implementation on AWS Trainium via the Neuron Kernel Interface (NKI).

<p align="center">
  <img src="https://upload.wikimedia.org/wikipedia/commons/a/ad/Smith-Waterman-Algorithm-Scoring-1.png" width="400"/>
  <br/>
  <em>Figure 1: Data dependencies in Smith–Waterman algorithm. <br/>
    Source: Wikipedia.</em>
</p>

## The challenge
One challenge is the algorithm itself. Smith-Watterman is traditionally a very iterative algorithm - there do exist CUDA implementations of it that are parallelized well, but our first challenge will be constructing an efficient version of the algorithm that can run in our systolic array setting. Part of this will be working within the contraints of the NeuronCore chips, mainly constructing tiles corectly (they must be oriented correctly the "P" dimension restricted to a max length of 128, and within the restricted fast memory), and another will be ensuring that we can have appropriate parallel tasks while still achieving correctness with appropriate synchronization, since there are few synchronization primitives.

## Resources
...

## Goals and deliverables
...

## Platform choice
We'd like to use [AWS's Trainium](https://aws.amazon.com/ai/machine-learning/trainium/) platform. This platform uses Trainium chips, which use the NeuronCore architecture and are [based off of systolic arrays](https://aws.amazon.com/blogs/machine-learning/how-to-extend-the-functionality-of-aws-trainium-with-custom-operators/). The combination of multiple engines, fast interconnect, and multiple cores, makes these a perfect chip for running systolic array workloads.

We'll be using the [Neuron Kernel Interface (NKI) language](https://awsdocs-neuron.readthedocs-hosted.com/en/latest/general/nki/index.html) to construct our implementation, which provides some low-level contructs to make full-use of the NeuronCores. The library provides both high-level collective-communication abstractions and low-level CUDA-like reduction operations.

## Schedule
...
