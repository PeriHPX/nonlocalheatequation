# Nonlocal Heat Equation [![CircleCI](https://circleci.com/gh/PeriHPX/nonlocalheatequation.svg?style=shield)](https://circleci.com/gh/PeriHPX/nonlocalheatequation) [![Codacy Badge](https://app.codacy.com/project/badge/Grade/272852a564334bf2acfc9b43eb3572f7)](https://www.codacy.com/gh/PeriHPX/nonlocalheatequation/dashboard?utm_source=github.com&amp;utm_medium=referral&amp;utm_content=PeriHPX/nonlocalheatequation&amp;utm_campaign=Badge_Grade) [![Coverage Status](https://coveralls.io/repos/github/PeriHPX/nonlocalheatequation/badge.svg?branch=main)](https://coveralls.io/github/PeriHPX/nonlocalheatequation?branch=main)

<img src="https://avatars0.githubusercontent.com/u/1780988?s=280&v=4" align="right" width="130" />
<img src="https://summerofcode.withgoogle.com/static/img/og-image.png" align="right" width="130" />

**Documentation** : [main](https://perihpx.stellar-group.org/nonlocalheatequation/documentation/)

**Docker image**  : [main image](https://hub.docker.com/repository/docker/perihpx/nonlocalheatequation)

## About

This repository implements a distributed non-local heat equation solver using [HPX](https://github.com/STEllAR-GROUP/hpx) as a part
 of work done in [Google Summer of Code 2020](https://summerofcode.withgoogle.com/projects/#6693763189047296). This code is mostly used to experiment with new HPX features or
add new features to the non-local models. The code also implements **Domain decomposition** and **Load balancing** for rectangle domains. The latest [documentation](https://perihpx.github.io/nonlocalheatequation/documentation/) and a [Docker image](https://hub.docker.com/repository/docker/perihpx/nonlocalheatequation) with the latest stable version are available as well. 

## Project Summary
Recently various nonlocal models have been applied for understanding of complex spatially multiscale phenomena like solid mechanics, fluid mechanics, particulate media, directed self-assembly of Block-Copolymer, etc. Nonlocal models have dependence of a single point on a small set of near neighboring points, which hints for data-level parallelism. We utilize a modern parallelization library, [HPX](https://github.com/STEllAR-GROUP/hpx), to develop the parallel solver. The goal of the proposed solution is to achieve full distribution of workload starting from mesh partition to computation of fields at mesh nodes. The following goal is achieved step by step, starting from a simple sequential implementation of [1D nonlocal diffusion](https://github.com/perihpx/nonlocalheatequation/blob/master/src/1d_nonlocal_serial.cpp). Ideas of 1D nonlocal serial equation are extended to [2D nonlocal serial implementation](https://github.com/perihpx/nonlocalheatequation/blob/master/src/2d_nonlocal_serial.cpp). The 2D nonlocal diffusion equation is made multi-threaded to enable concurrent processing within a single node in the [2D nonlocal asynchronous implementation](https://github.com/perihpx/nonlocalheatequation/blob/master/src/2d_nonlocal_async.cpp). The 2D nonlocal diffusion equation is then made completely distributed to exploit data level parallelism in [2D nonlocal distributed implementation](https://github.com/perihpx/nonlocalheatequation/blob/master/src/2d_nonlocal_distributed.cpp). The 2D nonlocal distributed implementation also implements **Load balancing** algorithm. **Domain decomposition** for the rectangle domain is implemented as a seperate [tool](https://github.com/perihpx/nonlocalheatequation/blob/master/src/domain_decomposition.cpp).

## Folder information
*   [`data`](https://github.com/perihpx/nonlocalheatequation/tree/master/data) contains sample mesh data used to test the **Domain Decomposition** tool.
*   [`description`](https://github.com/perihpx/nonlocalheatequation/tree/master/description) contains the **latex** code for building the project description.
*   [`docs`](https://github.com/perihpx/nonlocalheatequation/tree/master/docs) contains the **Cmake** file for building the documentation.
*   [`include`](https://github.com/perihpx/nonlocalheatequation/tree/master/include) contains various header files used in the nonlocal heat equation implementation
*   [`src`](https://github.com/perihpx/nonlocalheatequation/tree/master/src) contains the source files for implementation ranging from serial 1d equation to fully distributed 2d nonlocal heat equation.
*   [`tests`](https://github.com/perihpx/nonlocalheatequation/tree/master/tests) contains the **test** files used to test the correctness of various implementations of the nonlocal heat equation implementation and testing the **Load Balancing** algorithms.

## Source files information
*   [`src/1d_nonlocal_serial.cpp`](https://github.com/perihpx/nonlocalheatequation/blob/master/src/1d_nonlocal_serial.cpp) implements serial implementation of 1D nonlocal heat equation.
*   [`src/2d_nonlocal_serial.cpp`](https://github.com/perihpx/nonlocalheatequation/blob/master/src/2d_nonlocal_serial.cpp) implements serial implementation of 2D nonlocal heat equation.
*   [`src/2d_nonlocal_async.cpp`](https://github.com/perihpx/nonlocalheatequation/blob/master/src/2d_nonlocal_async.cpp) implements asynchronous, thread level parallel implementation of 2D nonlocal heat equation.
*   [`src/2d_nonlocal_distributed.cpp`](https://github.com/perihpx/nonlocalheatequation/blob/master/src/2d_nonlocal_distributed.cpp) implements fully distributed, data level parallel implementation of 1D nonlocal heat equation.
*   [`src/domain_decomposition.cpp`](https://github.com/perihpx/nonlocalheatequation/blob/master/src/domain_decomposition.cpp) implements the domain decomposition as a tool.

## Dependencies and version
Following versions have been used and verified to be supported by the code:

*   [CMake](https://cmake.org/) version : `3.10` or beyond
*   [VTK](https://vtk.org/) version : `8.2.0`
*   [GMSH](http://gmsh.info/) version : `4.7.0`
*   [METIS](http://glaros.dtc.umn.edu/gkhome/metis/metis/overview) version : `5.1.0`
*   [HPX](https://github.com/STEllAR-GROUP/hpx) version : `1.4.1`
    *   [BOOST](https://www.boost.org/) version : `1.71.0`
    *   [HWLOC](https://www.open-mpi.org/projects/hwloc/) version : `1.11.13`

## Building and Installation
```bash
mkdir build
cd build 
cmake .. -DCMAKE_PREFIX_PATH= /path/to/hpx/installation
make all
```

## Running tests
```bash
make test
```

## Quick Start
### Domain decomposition Tool
```bash
./2d_domain_decomposition ../data/400x400.msh data_4.txt 4
```

### Distributed 2d nonlocal equation
```bash
srun -n 4 -c 1 -N 4 ./2d_nonlocal_distributed --file data_4.txt --dt 0.00001 --nt 20 --eps 8 --nx 20 --ny 20 --npx 20 --npy 20 --dh 0.0025
```

### Test Load balancing
```bash
srun -n 4 -c 1 -N 4 ./2d_nonlocal_distributed --file ../tests/load_balance_25s_4n.txt --dt 0.00001 --nt 45 --eps 8 --nx 20 --ny 20 --npx 5 --npy 5 --dh 0.0025 --test 0 --test_load_balance --nbalance 10
```

## Student Developer
*   [Pranav Gadikar](https://www.linkedin.com/in/pranav-gadikar-2a0a21143/?originalSubdomain=in)

## Mentors
*   [Patrick Diehl](https://www.diehlpk.de) 

*   [Prashant K. Jha](https://www.math.lsu.edu/~jha/)
