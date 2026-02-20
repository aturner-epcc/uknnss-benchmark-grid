# UK NSS Grid benchmark


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


<!--
### Architectures

- CPU: x86, Arm
- GPU: NVIDIA, AMD, Intel
-->

### Languages and programming models

- Programming languages: C++
- Parallel models: MPI, OpenMP
- Accelerator offload models: CUDA, HIP, SYCL

## Building the benchmark

**Important:** All results submitted should be based on the following repository commits:

- grid-benchmark repository: [c7457a8](https://github.com/aportelli/grid-benchmark/commit/c7457a85b6a0d9d1578838af11477cb41b1a5764)
- Grid repository: [6165931](https://github.com/paboyle/Grid/commit/6165931afaa53a9885b6183ff762fc2477f30b51)

Benchmarks data are to be submitted for two code versions:
A vanilla baseline build, where no source code is altered subject to changes enlisted below or discussed with the procurement team, 
and an optimised build, where the benchmarking team is allowed to allow the code at will.
In both cases, any modifications made to the source code for the baseline build or the optimised build must be 
shared as part of the offerer submission.

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
- For NVIDIA GPUs, CUDA versions 11.x or 12.x are recommended. Teams recently reported that some code parts struggle with CUDA 13. Such code parts can be removed (subject to documentation) as long as they do not affect the benchmark outcome. 
- For AMD GPUs, ROCm version 6.x is recommended since Grid is incompatible with ROCm
  version 7.x without minor code modifications. If only ROCm 7.x is available, we provide
  a workaround below.

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
Any submitted benchmark must enlist a publicly visible pull/merge request issued by the benchmarking team that contains all changes, 
i.e. the same (altered) code base as to be used for all benchmark runs.
As a consequence, the benchmarking team is encouraged to start benchmarking using the latest software version.
The assessment team furthermore appreciates a description of any changes implemented by the benchmarking team.


### Build instructions

Detailed build instructions can be found in the benchmark source code
repository at:

- [https://github.com/aportelli/grid-benchmark/blob/main/Readme.md]

<!--
The benchmark code uses the [pixi](https://pixi.prefix.dev/latest/) package
manager to manage the build process for the software and its dependencies. 
-->

Example build configurations are provided for:

- [Tursa (EPCC, Scotland)](https://epcced.github.io/dirac-docs/tursa-user-guide/hardware/): CUDA 11.4, GCC 9.3.0, OpenMPI 4.1.1, UCX 1.12.0
   + NVIDIA A100 GPU, NVLink, Infiniband interconnect
- [Daint (CSCS, Switzerland)](https://docs.cscs.ch/clusters/daint/): CUDA 12.4, GCC 14.2, HPE Cray MPICH 8.1.32
   + NVIDIA GH200 CPU+GPU, NVLink, Slingshot 11 interconnect
- [LUMI-G (CSC, Finland)](https://docs.lumi-supercomputer.eu/hardware/lumig/): ROCm 6.0.3, AMD clang 17.0.1, HPE Cray MPICH 8.1.23 (custom)
   + AMD MI250X GPU, Infinity fabric, Slingshot 11 interconnect
 
Further reference builds are available from [https://github.com/paboyle/Grid/tree/develop/systems](https://github.com/paboyle/Grid/tree/develop/systems).

## Running the benchmark

The benchmark descriptions below enlist mandatory command line flags.
The output of the `Benchmark_Grid` code is controlled via the `--json-out` option. 
All details to be reported are to be taken from this output file.

### Benchmark execution

Besides the mandatory flags, Grid has many command-line interface flags that control its runtime behaviour. Identifying
the optimal flags, as with the compilation options, is system-dependent and requires
experimentation. A list of Grid flags is given by passing `--help` to `grid-benchmark`, and a
full list is provided for both Grid and grid-benchmark in the [grid-benchmark README](https://github.com/aportelli/grid-benchmark/).

For the acceptance tests, all benchmark configurations are to be submitted via scheduler scripts.
The scripts may contain arbitrary system and benchmark settings (e.g. additional environment variables or command line instructions),
but have to be run on the vanilla system without any benchmark-specific reconfiguration.
Notably, rebooting the acceptance system for a particular benchmark with particular settings is not permitted.

<!--
The submission scripts should be written to accurately allocate NUMA affinities,
GPU indices, CPU thread indices, and any necessary environment variables (such
as GPU-GPU communication settings, e.g. for UCX) for the specific system and software stack in
use, using a wrapper script if necessary. 
-->

There are example job submission scripts and launch 
wrapper scripts in the
[grid-benchmark systems directory](https://github.com/aportelli/grid-benchmark/tree/main/systems)).
There are also run scripts for specific systems that may be closer to the target
architecture in the [Grid systems directory](https://github.com/paboyle/Grid/tree/develop/systems).

<!--
#### Command-line arguments


Important command line options:

- A critical `Grid` flag is `--accelerator-threads`. This heavily influences
  the warp/wavefront occupancy by multiplying Grid's default numbers of threads per
  thread block in a GPU kernel launch. Setting `--accelerator-threads 8` is generally
  optimal, but this may vary between hardware and is one of the first things that
  should be tested. CPU thread counts per rank are set separately with the `--threads`
  flag.

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

A single GPU should be allocated per MPI rank (or GCD in the case of e.g. MI250X).
The subdirectories in the [benchmark systems directory](https://github.com/aportelli/grid-benchmark/tree/main/systems)
have example wrapper scripts for how to do this.
-->

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
Domain-wall fermion benchmarks for 24^4 and 48^4 local volumes, which are
representative of typical production runs. With `jq`, this can be extracted
from the result JSONs as:

```
jq '.flops | .comparison_point_Gflops' path/to/json
```

or using any other JSON-parser of choice. This is given in units of GFlops/s/node.

### Required data

Data for the following table have to be provided.
For each and every run, the result data have to be correct (mandatory requirement). 
Performance data feed into a weighted total average.

| Command line options | Nodes/GPUs | Baseline FoM | Optimised code FoM | Comments | Min performance baseline |
|--:|--:|--:|--:|:--|:--|
| ``--mpi 1.1.1.1 --max-L 48`` | 1/1 | | | Single GPU throughput | xxx |
| ``--mpi 1.1.1.4 --max-L 48`` | 1/4 | | | Assuming a n=4 four GPU per node configuration. Otherwise, provide 1.1.1.n data. | xxx |
| ``--mpi 1.2.4.4 --max-L 48`` | 8/32 | | | Assuming a n=4 four GPU per node configuration. Otherwise, provide 1.1.1.n data. | xxx |
| ``--mpi 1.4.4.4 --max-L 48`` | 16/64 | | | Assuming a n=4 four GPU per node configuration. Otherwise, provide 1.1.1.n data. | xxx |
| ``--mpi 2.4.4.4 --max-L 48`` | 32/128 | | | Assuming a n=4 four GPU per node configuration. Otherwise, provide 1.1.1.n data. | xxx |
| ``--mpi 4.4.4.4 --max-L 48`` | 64/256 | | | Assuming a n=4 four GPU per node configuration. Otherwise, provide 1.1.1.n data. | xxx |
| ``--mpi ... --max-L 48`` | | | | If island/subpartition with higher bandwidth is provided, pick configuration fitting to this island. | |
| ``--mpi ... --max-L 48`` | | | | Whole system run | |


<!--
To be a valid figure-of-merit, the following conditions must be met:

- `Grid` and `Benchmark_Grid` must be compiled with the commits stated above
  and must meet any source code modification restrictions stated above
- MPI ranks must be distributed as described above
- For the command-line options specific to `Benchmark_Grid`, only `--json-out`
  may be used (i.e. do not disable any benchmarks)
- The benchmark must be run on the minimum number of GPU/GCD described above



| Daint nodes | Total GPU | `--mpi` option | FoM (Comparison Point Gflops/s) |
|--:|--:|--:|--:|
| 4 | 16 | 1.1.4.4 | 19770 |
| 8 | 32 | 1.2.4.4 | 11198 |
| 16 | 64 | 1.4.4.4 | 9389* |
| 32 | 128 | 2.4.4.4 | 7388 |
| 64 | 256 | 4.4.4.4 | 5862 |


## Reporting Results

Note that the benchmark will generate more output data than is
requested, the offeror needs only to report the benchmark values
requested. Additional data may be provided if desired.

For both the baseline build and the optimised build. The offeror should
provide copies of:

- Details of any modifications made to the `Grid` or `Benchmark_Grid` source code
  released under the same licence as the software itself
- The compilation process and configuration settings used for the benchmark results - 
  including makefiles, compiler versions, dependencies used and their versions or
  Spack environment configuration and lock files if Spack is used
- The job submission scripts and launch wrapper scripts used (if any)
- A list of options passed to the benchmark code
- The JSON results files from running the benchmarks
-->

## Reference data

The tables below enlist reference data from existing machines. 
Please note that these machines have a weaker interconnect (typically 200 GB/s) and use a characteristic local lattice size of 36^4.
All data results from the baseline code without any manual tuning.

### CSCS Daint

To aid in testing, we provide FoM values for varying problem sizes on
the [CSCS Daint system](https://docs.cscs.ch/clusters/daint/) below.
Daint nodes have 4x NVIDIA GH200 per node. 

In all cases, 1 MPI process per GPU was used and 72 CPU OpenMP threads
per MPI process.

| Daint nodes | Total GPU | `--mpi` option | FoM (Comparison Point Gflops/s) |
|--:|--:|--:|--:|
| 4 | 16 | 1.1.4.4 | 19770 |
| 8 | 32 | 1.2.4.4 | 11198 |
| 16 | 64 | 1.4.4.4 | 9389* |
| 32 | 128 | 2.4.4.4 | 7388 |
| 64 | 256 | 4.4.4.4 | 5862 |

Full output from these reference runs are available at:
[https://github.com/aportelli/grid-benchmark/tree/main/results/251124/daint]

The reference FoM was determined
by running the reference problem on 64 Daint GH200 (16 GPU nodes)
with 1 MPI process per GPU and 72 OpenMP CPU threads per MPI process.
and is marked by a *.

<!--
The projected FoM (Comparison Point) for the target problem on the target system
must not be lower than this value.
-->



## License

This benchmark description and any associated files are released under the
MIT license.


<!--
### Required Tests

- **Target configuration:** Benchmark_Grid should be run on a minimum of *128 GPU/GCD*.
- **Reference FoM:** The reference FoM is from the CSCS Daint system using 64 GPU (16 nodes) is `*9389 Gflops/s*.
   + [JSON ("result.json") output from the reference run](https://github.com/aportelli/grid-benchmark/blob/main/results/251124/daint/benchmark-grid-16.2128747/result.json)

**Important:** For the both the baseline build and the optimised build, the projected FoM submitted 
must give at least the same performance as the reference value.

-->

