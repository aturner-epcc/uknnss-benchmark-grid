# UK NSS GRID benchmark

grid-benchmark is the benchmarking package, available at https://
github.com/aportelli/grid-benchmark. It is licensed under GPLv2, with a list of
contributors available at https://github.com/aportelli/grid-benchmark/graphs/contributors.
The benchmark uses the underpinning Grid C++ 17 library for lattice QCD applications.

Note: the repository contains two benchmarks: Benchmark_Grid and Benchmark_IO. Only
Benchmark_Grid is in scope for this procurement.

- Benchmark_Grid: A sparse Dirac matrix performance benchmark which also performs
  independent memory and inter-process communication benchmarks, alongside a correctness
  check.

In summary, Benchmark_Grid benchmarks three discretisations of the Dirac matrix: "Wilson",
"domain-wall" (or DWF4), and "staggered". For benchmarking purposes, the only differences
between these discretisations is the flop count per Dirac matrix application and that domain-wall
has an additional local dimension of size Ls = 12, increasing its memory requirements. The sparse
Dirac matrix benchmark is ran for five problem sizes, each of which assigns a 4D array to each MPI
rank, referred to as the local lattice size or local volume. These are 8^4, 12^4, 16^4, 24^4, and
32^4. Since the local volumes are fixed, increasing the number of MPI ranks corresponds to a
weak scaling of the benchmark.

## Status

Stable

## Maintainers

- Antonin Portelli
- Ryan Hill

## Overview

### Software

[https://github.com/aportelli/grid-benchmark](https://github.com/aportelli/grid-benchmark)

### Architectures

- CPU: x86, Arm
- GPU: NVIDIA, AMD, Intel

### Languages and programming models

- Programming languages: C++
- Parallel models: MPI, OpenMP
- Accelerator offload models: CUDA, HIP

## Building the benchmark

### Permitted modifications

TBC

### Manual build

Detailed build instructions can be found in the benchmark source code
repository at:

- [https://github.com/aportelli/grid-benchmark/blob/main/Readme.md]

Example build configurations are provided for:

- Tursa: CUDA 11.4, GCC 9.3.0, OpenMPI 4.1.1, UCX 1.12.0
- Daint: CUDA 12.4, GCC 14.2, HPE Cray MPICH 8.1.32
- LUMI: ROCm 6.0.3, AMD clang 17.0.1, HPE Cray MPICH 8.1.23 (custom)
- Durham GPU testbed: ROCm 7.0.1, AMD clang 20.0.0, OpenMPI 5.0.9, UCX 1.19.0

## Running the benchmark

### Required Tests

Target configuration: Grid_Benchmark should be run on a minimum of X GPU/GCD. 

Reference FoM: from the Tursa system using 64 nodes (512 GPU) is Y.

The projected FoM submitted must give at least the same performance 
as the reference value.

To aid in testing, we provide FoM values for varying problem sizes on
Tursa below. Tursa nodes have 2x AMD ?? EPYC CPU and 4x NVIDIA A100 GPU. 

| Tursa nodes | Total GPU | Parallel decomposition | FoM (Comparison Point Gflops) |
|--:|--:|--:|--:|
| 1 | 4 | 1.1.1.4 |   |
| 2 | 8 | 1.1.2.4 |  |
| 64 | 512 | 4.4.4.4 |   |

### Benchmark execution

Examples of job scripts that run the required tests
are located in the `run` directory.
The job scripts should be edited to reflect
the architecture of the target system as follows:




## Reporting Results

Note that the benchmark will generate more output data than is
requested, the offeror needs only to report the benchmark values
requested. Additional data may be provided if desired.

The offeror should provide a copy of the Makefile and configuration
settings used for the benchmark results.

The benchmark should be compiled and run on the compiler and MPI
environment that will be provided on the proposed machine.

## Example performance data report

### Tursa



## License

This benchmark description and associated files are released under the
MIT license.
