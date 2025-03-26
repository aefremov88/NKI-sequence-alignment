## 15-618 Final Project

[Link](https://aefremov88.github.io/NKI-sequence-alignment/)

## Team members
Kandasamy Chokkalingam <kchokkal@andrew.cmu.edu>

Anton Efremov <aefremov@andrew.cmu.edu>

## Summary
In this document, we propose implementing the **Smith–Waterman algorithm** for local sequence alignment using the **systolic array parallel programming model** on AWS Trainium via the AWS Neuron Kernel Interface (NKI). This algorithm features a 2D data passing pattern, which presents a strong opportunity for parallelization on systolic array-based systems.

## Background
The Smith–Waterman algorithm solves the problem of local sequence alignment: given two sequences, it identifies the best matching pair of substrings by computing an optimal alignment score based on a user-defined scoring scheme. This includes a reward for character matches, penalties for mismatches, and gap penalties for insertions or deletions. It plays a foundational role in bioinformatics, identifying similar regions between DNA, RNA, or protein sequences. Due to its quadratic time complexity and the massive scale of modern sequencing datasets—often involving sequences with billions of elements—Smith–Waterman becomes a major computational bottleneck in genomic pipelines. There are GPU-accelerated libraries, such as CUDASW++, that take advantage of parallelism to achieve large speedups.

The algorithm's structure makes it well-suited for systolic arrays, as it involves computing a 2D dynamic programming matrix where each cell $H_{i,j}$ depends only on its top, left, and top-left neighbors. As shown in the diagram, each cell is computed as the maximum of the match/mismatch score from the diagonal and possible insertion or deletion scores from the top or left, adjusted by gap penalties. This regular, local data dependency pattern aligns perfectly with the systolic array model, where processing elements (PEs) are arranged in a 2D grid and communicate only with neighbors in a rhythmic, pipelined fashion. Each PE can compute one cell and pass necessary values to adjacent PEs. This makes the Smith–Waterman algorithm an excellent candidate for implementation on AWS Trainium via the Neuron Kernel Interface (NKI).

<p align="center">
  <img src="https://upload.wikimedia.org/wikipedia/commons/a/ad/Smith-Waterman-Algorithm-Scoring-1.png" width="400"/>
  <br/>
  <em>Figure 1: Data dependencies in Smith–Waterman algorithm. <br/>
    Source: Wikipedia.</em>
</p>

## The challenge
One challenge is the algorithm itself. Smith-Watterman is traditionally a very iterative algorithm - there do exist CUDA implementations of it that are parallelized well, but our first challenge will be constructing an efficient version of the algorithm that can run in our systolic array setting. Part of this will be working within the contraints of the NeuronCore chips, mainly constructing tiles corectly (they must be oriented correctly the "P" dimension restricted to a max length of 128, and within the restricted fast memory), and another will be ensuring that we can have appropriate parallel tasks while still achieving correctness with appropriate synchronization, since there are few synchronization primitives. In addition, the NeuronCore chips come with 4 engines, each optimized for a different purpose. For example, the GPSIMD engine allows for very fast parallel computation across a very limited array size, while the Vector engine effciently computes vector operations - for example, one specific tradeoff we'll have to investigate here is if moving data from the GPSIMD engine to the Vector engine is worth it to use the built-in scan operator on the Vector engine. The data movement may take longer than the extra time the scan operation saves.

In fact, managing the memory hierarchy will be, in our current perspective, one of the most interesting facets of this project. The NeuronCore architecture has multiple levels of memory hierarchy - within a processor and across them - and they've just released an even more [low level primitive](https://awsdocs-neuron.readthedocs-hosted.com/en/latest/general/nki/nki_direct_allocation_guide.html) to more directly work with memory to be even faster., so we'll need to pay very close attention about how we tailor our algorithm to decompose the problem well.

In terms of dependencies, to calculate a specific (i,j) cell, as shown in the above diagram, we need the cells previous to it in the row and the cell previous to in the column, as well as the (i-1,j-1) cell. This has a much higher degree of dependencies than previous problems we've implemented in 418, but the key is that this is well-suited to systolic arrays. The locality, in the traditional sense, is good across a row, but poor since we need to access values in previous columns. However - since we're using systolic arrays, we can decompose the problem into "tiles" (how NKI calls them), and each tile only needs the values bordering it. There is no divergent execution to worry about since we execute the same steps for each cell, which means we will not have to worry too much about workload balance. In addition, the communication to computation ratio is high since for a cell, on average, we need to take so many max's across the array and for each cell. However, since our platform allows us to execute in a SPMD way, this is less of a concern, since we can operate across multiple data at once.

## Resources
We'd like to use [AWS's Trainium](https://aws.amazon.com/ai/machine-learning/trainium/) platform. This platform uses Trainium chips, which use the NeuronCore architecture and are [based off of systolic arrays](https://aws.amazon.com/blogs/machine-learning/how-to-extend-the-functionality-of-aws-trainium-with-custom-operators/). The combination of multiple engines, fast interconnect, and multiple cores, makes these a perfect chip for running systolic array workloads. We'd need to get access (likely credits) to the Trn1 AWS Trainium machines.

We'll be using the [Neuron Kernel Interface (NKI) language](https://awsdocs-neuron.readthedocs-hosted.com/en/latest/general/nki/index.html) to construct our implementation, which provides some low-level contructs to make full-use of the NeuronCores. The library provides both high-level collective-communication abstractions and low-level CUDA-like reduction operations.

We'll be starting from scratch, although there is the CUDA library for SW mentioned above. However, we believe our implementation will have to be much more different in order to make full use of the NeuronCore architecture. The SW algorithm is well documented in the original paper, however, and there many extensions.

Smith, T. F., & Waterman, M. S. (1981). Identification of common molecular subsequences. Journal of molecular biology, 147(1), 195–197. https://doi.org/10.1016/0022-2836(81)90087-5

## Goals and deliverables
**75%-goal**: Successfully implement the Smith–Waterman algorithm using the systolic array parallel programming model on AWS Trainium via the Neuron Kernel Interface (NKI). Demonstrate maximum parallelism from this model by designing an efficient dataflow and pipeline synchronization strategy.

**100%-goal**: Achieve the 75% goal and run the implementation on a dataset that **approximates real-world usage** (e.g., bioinformatics datasets). Perform detailed profiling to understand performance characteristics, resource utilization, and parallel throughput for **real-world dataset** on the Trainium platform. Specifically, what is our final computation to communication ratio? How much time are spending moving data? What is the final runtime and how does it scale with the sequence length (i.e. are systolic arrays a good solution for this problem, as the theory suggests they should be).

**125%-goal**: Compare the profiling results and performance bottlenecks of our systolic implementation with a **high-performance CUDA-based Smith–Waterman library** (such as CUDASW++). Analyze differences in hardware efficiency, data movement costs, and parallel scalability between the systolic and CUDA execution models **OR** Extend our implementation to work for multiple sequence alignment (MSA), in which existing solutions use other, less accurate algorithms, because of the inability for Smith-Watterman on existing platforms to scale (it has complexity $L^n$ for $n$ sequences of max length $L$, but the best quality).

## Platform choice

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
