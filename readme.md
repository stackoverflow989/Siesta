# README

## Requirements

This project relies on Performance Application Programming Interface(PAPI) to obtain hardware performance counter information. <https://bitbucket.org/icl/papi/wiki/Home>

This project depends on libunwind. <https://www.nongnu.org/libunwind/>

This project needs to install cvxopt, numpy, mpi4py in the python environment.


## Siesta: Our proxy-app generator

- **bench_py_ver:** Code generation.
- **constant.py:** Some constant definitions used in the program.
- **main.py:** Main function.
- **merge_main_rule.py:** Merge all the main rules.
- **MPI_define.py:** Some constants defined by MPI.
- **nonterminal_dict.py:** Merge dictionaries of non-terminals.
- **sequitur.py:** Implementation of the sequitur algorithm.
- **utils.py:** Implementation of some helper functions.
- **with_compute.py:** Use clustering-based method to compress computation information.


## Tools

#### mpiP-3.5: Tracing tool, modified from the original mpiP-3.5

- **mpiPi.h:** Some important global information is located in the mpiPi_t structure.
- **mpiPi.c:** The main operating logic of mpiP.
- **make-wrappers.py:** Add customized operations after intercepting MPI.
- **mpiP-wrappers.c:** Wrapped MPI generated by make-wrappers.py. Do not modify it directly.
- **papi_utils.h:** Some customized papi API.

#### OSU_bench: Benchmark to model MPI function execution time and communication volume

- **pt2pt:** Point to point MPI communication function.
- **collective:** Collective MPI blocking communication functions.
- **util:** Some utilities to generate executable files.


## Quick Start

1. Compile mpiP and dependent libraries to generate libmpiP.so.
2. Use LD_PRELOAD to load the modified mpip dynamic link library. Modify the environment variable PAPI_MON_EVENTS to record the hardware performance counter. An example is:

```shell
export PAPI_MON_EVENTS="PAPI_LST_INS:PAPI_L1_DCM:PAPI_TOT_INS:PAPI_TOT_CYC:PAPI_BR_CN:PAPI_BR_MSP"
export LD_PRELOAD=/path/to/libmpiP.so
```

3. Run the target application, generate np trace files [0:np].trace.
4. If the processor cannot record six hardware performance counter events in one execution, perform multiple executions and merge traces

```shell
mpirun -np $nprocs python splice.py -t /path/to/trace/ -o /path/to/trace_merged/
```

5. Run main.py and code_gen.py to compress traces and generate proxy-app.

```shell
mpirun -np $nprocs python main.py -t /path/to/trace/ -o /path/to/proxy/
mpirun -np $nprocs python code_gen.py -o /path/to/data_bucket
```

6. Compile the generated proxy-app and execute it for replay.

