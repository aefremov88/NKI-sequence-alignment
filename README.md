# NKI-sequence-alignment

## URL

## Team members
Kandasamy Chokkalingam <kchokkal@andrew.cmu.edu>

Anton Efremov <aefremov@andrew.cmu.edu>

## Summary
In this document, we propose implementing the **Smith–Waterman algorithm** for local sequence alignment using the **systolic array parallel programming model** on AWS Trainium via the AWS Neuron Kernel Interface (NKI). This algorithm features a 2D data passing pattern, which presents a strong opportunity for parallelization on systolic array-based systems.

## Background
...

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
