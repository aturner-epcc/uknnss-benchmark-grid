# UK NNSS Grid benchmark


Grid_Benchmark is the benchmarking package, available at [https://github.com/aportelli/grid-benchmark].
It is licensed under GPLv2, with a list of
contributors available at [https://github.com/aportelli/grid-benchmark/graphs/contributors].
The benchmark uses the underpinning Grid C++ 17 library for lattice QCD applications.

Note: the repository contains two benchmarks: Benchmark_Grid and Benchmark_IO. Only
Benchmark_Grid is subject of discussion here. 

In summary, Benchmark_Grid benchmarks three discretisations of the Dirac matrix. 
The sparse Dirac matrix benchmark assigns a 4D array to each MPI rank/GPU, referred to as the local lattice size or local volume.
It is ran for different problem sizes (e.g. 8^4, 12^4, 16^4, 24^4, ...) and reports an average single precision performance for the two biggest runs.
Since the local volumes are fixed, increasing the number of MPI ranks corresponds to a
weak scaling of the benchmark.

## Status

Stable

## Maintainers

- Antonin Portelli
- Ryan Hill

**Important:** Please do not contact the benchmark maintainers directly with any questions.
All questions on the benchmark must be submitted via the procurement response mechanism.

## Overview

### Software

[https://github.com/aportelli/grid-benchmark](https://github.com/aportelli/grid-benchmark)


## Building the benchmark

**Important:** All results submitted should be based on the following repository commits:

- grid-benchmark repository: [c7457a8](https://github.com/aportelli/grid-benchmark/commit/c7457a85b6a0d9d1578838af11477cb41b1a5764)
- Grid repository: [6165931](https://github.com/paboyle/Grid/commit/6165931afaa53a9885b6183ff762fc2477f30b51)

Benchmarks data are to be submitted for two code versions:
A vanilla baseline build, where no source code is altered subject to changes described
below or discussed with the procurement team,  and an optimised build, where the
benchmarking team is allowed to allow the code at will. In both cases, any modifications
made to the source code for the baseline build or the optimised build must be 
shared as part of the bidder submission.

### Baseline build

`Benchmark_Gird` has been written with the intention that no modifications to the source code
are required. <!--It is also intended to be run without the need for additional CLI parameters beyond
`--json-out` and those required by Grid, although a full list of CLI options are provided in the
[grid-benchmark README](https://github.com/aportelli/grid-benchmark/) if required.-->
However, source code modifications might be required in few cases. Below is a list
of permitted modifications:

- Only modify the source code to resolve unavoidable compilation or runtime errors. The
  [Grid systems directory](https://github.com/paboyle/Grid/tree/develop/systems) has many examples
  of configuration and run options known to work on a variety of systems in case of e.g. linking
  errors or runtime issues.
- For compilation on systems with only ROCm 7.x and greater available, it is permitted to use
  the workaround described below as a substitute for code modification. Workarounds
  of this nature are permitted if unresolvable compilation errors otherwise occur.
- For NVIDIA GPUs, CUDA versions 11.x or 12.x are recommended. If only CUDA 13 or more recent
  is available, we provide a workaround below.
- For AMD GPUs, ROCm version 6.x is recommended since Grid is incompatible with ROCm
  version 7.x without minor code modifications. If only ROCm 7.x is available, we provide
  a workaround below.

**CUDA 13+ workaround**

Create a new header file with the following contents:
 
```c++
#pragma once
#include <cuda/std/functional>
namespace cub
{
    using Sum = cuda::std::plus<>;
}
```
 
Add `-include /path/to/header/file` in the `CXXFLAGS` definition for the system in
`grid-config.json`.

**ROCm 7.x/hipBLAS 3.x workaround**

Both the current develop branch of Grid and the selected Grid benchmarking commit explicitly use
the hipBLAS 2.x types hipblasComplex and hipblasDoubleComplex. As of hipBLAS 3.x, which
is the version of hipBLAS included with ROCm 7.x, these types have been deprecated in favour of
hipComplex and hipDoubleComplex. This will cause a compilation failure of the form
error: use of undeclared identifier 'hipblasComplex'.

This can be worked around by adding

```
-DhipblasComplex=hipComplex -DhipblasDoubleComplex=hipDoubleComplex
```

to the `CXXFLAGS` argument passed to the `configure` command for Grid. This can be automated
using a custom preset for the automatic deployment scripts for Grid and grid-benchmark as
documented in the [grid-benchmark README](https://github.com/aportelli/grid-benchmark/).

### Optimised build

Any modifications to the source code are allowed as long as they are able to be provided
back to the community under the same licence as is used for the software package that is
being modified.
Any submitted benchmark must clearly point to a publicly visible pull/merge request
issued by the benchmarking team that contains all changes, i.e. the same (altered) code
base as to be used for all benchmark runs.

The assessment team furthermore appreciates a description of any changes implemented by the
benchmarking team.


### Build instructions

Detailed build instructions can be found in the benchmark source code
repository at:

- [https://github.com/aportelli/grid-benchmark/blob/main/Readme.md]

Example build configurations are provided for:

- [Tursa (EPCC, Scotland)](https://epcced.github.io/dirac-docs/tursa-user-guide/hardware/): CUDA 11.4, GCC 9.3.0, OpenMPI 4.1.1, UCX 1.12.0
   + NVIDIA A100 GPU, NVLink, Infiniband interconnect
- [Daint (CSCS, Switzerland)](https://docs.cscs.ch/clusters/daint/): CUDA 12.4, GCC 14.2, HPE Cray MPICH 8.1.32
   + NVIDIA GH200 CPU+GPU, NVLink, Slingshot 11 interconnect
- [LUMI-G (CSC, Finland)](https://docs.lumi-supercomputer.eu/hardware/lumig/): ROCm 6.0.3, AMD clang 17.0.1, HPE Cray MPICH 8.1.23 (custom)
   + AMD MI250X GPU, Infinity fabric, Slingshot 11 interconnect
 
Further reference builds are available from [https://github.com/paboyle/Grid/tree/develop/systems](https://github.com/paboyle/Grid/tree/develop/systems).

## Running the benchmark

The benchmark descriptions below specify mandatory command line flags.
The output of the `Benchmark_Grid` code is controlled via the `--json-out` option. 
All details to be reported are to be taken from this output file.

### Benchmark execution

The following conditions on options and runtime configuration must be adhered to for both 
the baseline and optiised build:

- The runtime performance is affected by the MPI rank distribution. MPI ranks are specified
  with the `Grid` option `--mpi X.Y.Z.T` flag. To be representative of realistic
  workloads, the following algorithm **must** be used for setting the MPI decomposition:
    1. Allocate ranks to T until it reaches 4, e.g. `--mpi 1.1.1.4`.
    2. Allocate ranks to Z until it reaches 4, e.g. `--mpi 1.1.4.4`.
    3. Allocate ranks to Y until it reaches 4, e.g. `--mpi 1.4.4.4`.
    4. Allocate ranks to X until it reaches 4, e.g. `--mpi 4.4.4.4`.
    5. If further ranks are required, continue to allocate evenly in powers of 2.
- While `Grid` options can be varied, the `Bnechmark_Grid` software should be run with no
  additional flags than `--json-out`, which will write the results of the benchmark to a
  JSON file.
- A single GPU should be allocated per MPI rank (or GCD in the case of e.g. MI250X).

Besides the mandatory flags, Grid has many command-line interface flags that control its
runtime behaviour. Identifying the optimal flags, as with the compilation options, is
system-dependent and requires experimentation. A list of Grid flags is given by passing
`--help` to `grid-benchmark`, and a full list is provided for both Grid and grid-benchmark
in the [grid-benchmark README](https://github.com/aportelli/grid-benchmark/).

For the acceptance tests, all benchmark configurations are to be submitted via scheduler scripts.
The scripts may contain arbitrary system and benchmark settings (e.g. additional environment
variables or command line instructions), but have to be run on the vanilla system without any 
benchmark-specific reconfiguration. Notably, rebooting the acceptance system for a particular 
benchmark with particular settings is not permitted.

There are example job submission scripts and launch wrapper scripts in the
[grid-benchmark systems directory](https://github.com/aportelli/grid-benchmark/tree/main/systems).
There are also run scripts for specific systems that may be closer to the target
architecture in the [Grid systems directory](https://github.com/paboyle/Grid/tree/develop/systems).

### Correctness results 

The correctness check for this package ensures that a Conjugate Gradient solve using the Dirac matrix
matches a known analytic expression. The Conjugate Gradient solver relies on repeated applications
of the Dirac matrix and will therefore produce solutions in disagreement with the analytic result if
the Dirac matrix is incorrectly implemented.

The `benchmark_grid` code automatically performs this correctness check. If the check fails, you
will see a message similar to:

```
Failed to validate free Wilson propagator:
||(result - ref)||/||(result + ref)|| >= 1e-8
```

### Performance results

The figure of merit (FoM) for `Benchmark_Grid` is the comparison point sparse Dirac
matrix multiplication flop rate. This is the average of the single-precision
Domain-wall fermion benchmarks for different local volumes, which are
representative of typical production runs. With `jq`, this can be extracted
from the result JSONs as:

```
jq '.flops | .comparison_point_Gflops' path/to/json
```

or using any other JSON-parser of choice. This is given in units of GFlops/s/node.

### Required data

- **Reference FoM:** The reference FoM is from the IsambardAI system using 128 GPU (32 nodes): *7766 Gflops/s per node*.
- **Target configuration:** The Grid performance must meet the same performance as the reference FoM on a minimum of 256 GPU/GCD.


Data for the following table have to be provided.

| Command line options | Nodes/GPUs | Baseline FoM | Optimised code FoM | Comments |
|--:|--:|--:|--:|:--|
| ``--mpi 1.1.1.1`` | 1/1 | | | Single GPU throughput |
| ``--mpi 1.1.1.4`` | 1/4 | | | Assuming a n=4 four GPU per node configuration. Adjust for more GPU per node, e.g. 8 GPU per node would be `--mpi 1.1.2.4`. |
| ``--mpi 1.2.4.4`` | 8/32 | | | Assuming a n=4 four GPU per node configuration. Adjust for more GPU per node. |
| ``--mpi 1.4.4.4`` | 16/64 | | | Assuming a n=4 four GPU per node configuration. Adjust for more GPU per node. |
| ``--mpi 2.4.4.4`` | 32/128 | | | Assuming a n=4 four GPU per node configuration. Adjust for more GPU per node.  |
| ``--mpi 4.4.4.4`` | 64/256 | | | Assuming a n=4 four GPU per node configuration. Adjust for more GPU per node.  |
| ``--mpi ...`` | | | | Whole system run |

Optionally, if an island/subpartition with higher bandwidth is provided, then the benchmark should
be run on the maximum possible size in this partition and the results reported in the same format
as the table above.

### Example performance data

To aid in testing, we provide FoM values for varying problem sizes on
the [CSCS Daint system](https://docs.cscs.ch/clusters/daint/) and
the [IsambardAI system](https://docs.isambard.ac.uk/specs/#system-specifications-isambard-ai-phase-2).
Both Daint nodes and IsambardAI have 4x NVIDIA GH200 per node and
4x 200 Gbps Slingshot 11 interfaces per node. 

In all cases, 1 MPI process per GPU was used and 72 CPU OpenMP threads
per MPI process.

| Nodes | Total GPU | `--mpi` option | Daint FoM (Comparison Point Gflops/s per node) | IsambardAI FoM (Comparison Point Gflops/s per node) |
|--:|--:|--:|--:|--:|
| 4 | 16 | 1.1.4.4 | 19770 | 20201 |
| 8 | 32 | 1.2.4.4 | 11198 | 9066 |
| 16 | 64 | 1.4.4.4 | 9389 | 8739 |
| 32 | 128 | 2.4.4.4 | 7388 | 7766* |
| 64 | 256 | 4.4.4.4 | 5862 | 6413 |
| 128 | 512 | 4.4.4.8 | | 6040 |
| 256 | 1024 | 4.4.8.8 | | 5271 |
| 512 | 2048 | 4.8.8.8 | | 4504 |

## Reporting Results

For both the baseline build and the optimised build. The offeror should
provide copies of:

- Details of any modifications made to the `Grid` or `Benchmark_Grid` source code
  released under the same licence as the software itself
- The compilation process and configuration settings used for the benchmark results - 
  including makefiles, compiler versions, dependencies used and their versions or
  Spack environment configuration and lock files if Spack is used
- The job submission scripts and launch wrapper scripts used (if any)
- A list of options passed to the benchmark code
- All output files from running the benchmarks

## License

This benchmark description and any associated files are released under the
MIT license.


