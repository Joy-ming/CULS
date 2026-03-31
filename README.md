# CULS
CULS is a GPU-based logic synthesis tool developed by the research team 
supervised by Prof. Evangeline F. Y. Young at The Chinese University of Hong Kong (CUHK).

## Dependencies
* CMake >= 3.8
* GCC >= 7.5.0
* CUDA >= 11.4

## Building
* Build as a standalone tool:
    ```bash
    mkdir build && cd build
    cmake ..
    make
    ```
    The built binary executable will be named `gpuls`. 

* Build as a patch of ABC:
    ```bash
    mkdir build && cd build
    cmake .. -DPATCH_ABC=1
    make
    ```
    The built binary executable will be named `abcg`. 

    If the readline library is installed in a custom path on your machine,
    add the option `-DREADLINE_ROOT_DIR=<readline_path>` when invoking cmake.
    CULS can still be successfully built even if the readline library 
    is not found.

## Getting started

* Standalone mode

    To interact with the command prompt, run
    ```bash
    ./gpuls
    ```
    
    You can also directly execute a script, e.g., 
    ```bash
    ./gpuls -c "read ../abc/i10.aig; resyn2; write i10_resyn2.aig"
    ```
* ABC patch mode

    The usage is the same as ABC. For instance, 
    ```bash
    ./abcg -c "read ../abc/i10.aig; gget; gresyn2; gput; print_stats; cec -n"
    ```

## Commands

* Standalone mode
    * `read`: read an AIG from a file
    * `write`: dump the internal AIG to a file
    * `b`: AIG balancing
    * `rw`: AIG rewriting
    * `rf`: AIG refactoring
    * `rs`: AIG resubstitution
    * `st`: strashing and dangling-node removal
    * `resyn2`: perform the resyn2 optimization script
    * `resyn2rs`: perform the resyn2rs optimization script
    * `ps`: print AIG statistics
    * `time`: print time statistics

* ABC patch mode

    Standalone mode commands will be prefixed by `g`, e.g., `grf`
    for AIG refactoring. 

    Additionally, there are two commands `gget` and `gput` for converting the
    AIG data structure from ABC to GPU, and from GPU to ABC, respectively,
    similar to the ABC9 package. 

## File Structure

```
CULS/
├── CMakeLists.txt
├── LICENSE
├── README.md
├── include/                                  # Third-party header-only libraries
│   ├── CLI11.hpp                             # Command-line parsing library
│   ├── robin_hood.h                          # Robin Hood hash map
│   └── cli/                                  # CLI library (interactive shell)
│       ├── LICENSE
│       ├── boostasiocliasyncsession.h
│       ├── boostasioremotecli.h
│       ├── boostasioscheduler.h
│       ├── cli.h
│       ├── clifilesession.h
│       ├── clilocalsession.h
│       ├── colorprofile.h
│       ├── filehistorystorage.h
│       ├── historystorage.h
│       ├── loopscheduler.h
│       ├── scheduler.h
│       ├── standaloneasiocliasyncsession.h
│       ├── standaloneasioremotecli.h
│       ├── standaloneasioscheduler.h
│       ├── volatilehistorystorage.h
│       └── detail/                           # Internal implementation details
│           ├── boostasiolib.h
│           ├── commonprefix.h
│           ├── fromstring.h
│           ├── genericasioremotecli.h
│           ├── genericasioscheduler.h
│           ├── genericcliasyncsession.h
│           ├── history.h
│           ├── inputdevice.h
│           ├── inputhandler.h
│           ├── keyboard.h
│           ├── linuxkeyboard.h
│           ├── newboostasiolib.h
│           ├── newstandaloneasiolib.h
│           ├── oldboostasiolib.h
│           ├── oldstandaloneasiolib.h
│           ├── rang.h
│           ├── server.h
│           ├── split.h
│           ├── standaloneasiolib.h
│           ├── terminal.h
│           └── winkeyboard.h
└── src/                                      # Source code
    ├── main.cpp                              # Entry point
    ├── common.h                              # Shared type definitions
    ├── aig_manager.cu / .h                   # Top-level AIG manager
    ├── command_manager.cpp / .h             # CLI command registration
    ├── aig/                                  # Core AIG data structure & GPU kernels
    │   ├── mffc.cuh                          # Maximum Fanout-Free Cone (MFFC)
    │   ├── strash.cu / .cuh                  # Structural hashing
    │   ├── traverse.cu / .cuh               # AIG traversal utilities
    │   └── truth.cu / .cuh                  # Truth table computation
    ├── algorithms/                           # Logic optimization algorithms
    │   ├── balance.cu / .h                   # AIG balancing
    │   ├── refactor.cu / .h                  # AIG refactoring
    │   ├── refactor_core.cu                  # Refactoring core kernels
    │   ├── refactor_mffc.cu                  # MFFC computation for refactoring
    │   ├── resub.cu / .h                     # AIG resubstitution
    │   ├── resub_core.cu                     # Resubstitution core kernels
    │   ├── resub_utils.h                     # Resubstitution utilities
    │   ├── rewrite.cu / .h                   # AIG rewriting
    │   ├── rewrite_library.inc               # Pre-computed rewriting library
    │   └── sop/                              # Sum-of-Products (SOP) utilities
    │       ├── alg_factor.cuh                # Algebraic factoring
    │       ├── minato_isop.cuh               # Minato ISOP algorithm
    │       └── sop.cuh                       # SOP representation
    ├── abc_patch/                            # ABC integration patch
    │   ├── abc_patch.cpp                     # Patch entry point
    │   ├── abc_patch_gpucmd.cpp              # GPU command bindings for ABC
    │   ├── abc_patch_int.h                   # Internal patch definitions
    │   └── abc_patch_transform.cpp           # AIG conversion between ABC and GPU
    ├── hash_table/                           # GPU hash table
    │   └── hash_table.h
    └── misc/                                 # Miscellaneous utilities
        ├── print.cu / .cuh                   # Debug/info printing
        ├── string_utils.h                    # String helper functions
        ├── tables.cuh                        # Lookup tables
        ├── truth_utils.cuh                   # Truth table utilities
        └── vectors.cuh                       # GPU vector utilities
```

## Publications
* Shiju Lin, Jinwei Liu, Tianji Liu, Martin D.F. Wong, Evangeline F.Y. Young, 
"NovelRewrite: Node-Level Parallel AIG Rewriting", 
59th ACM/IEEE Design Automation Conference (DAC), 2022.
* Tianji Liu, Evangeline F.Y. Young, "Rethinking AIG Resynthesis in Parallel", 
60th ACM/IEEE Design Automation Conference (DAC), 2023.
* Yang Sun, Tianji Liu, Martin D.F. Wong, Evangeline F.Y. Young, 
"Massively Parallel AIG Resubstitution", 
61st ACM/IEEE Design Automation Conference (DAC), 2024.
* Tianji Liu, Lei Chen, Xing Li, Mingxuan Yuan, Evangeline F.Y. Young, 
"FineMap: A Fine-grained GPU-parallel LUT Mapping Engine", 
29th Asia and South Pacific Design Automation Conference (ASP-DAC), 2024.
* Tianji Liu, Yang Sun, Lei Chen, Xing Li, Mingxuan Yuan, Evangeline F.Y. Young,
"A Unified Parallel Framework for LUT Mapping and Logic Optimization",
IEEE Transactions on Computer-Aided Design of Integrated Circuits 
and Systems (TCAD), 2024.
* Tianji Liu, Evangeline F.Y. Young, "Simulation-based Parallel Sweeping: 
A New Perspective on Combinational Equivalence Checking", 
62nd ACM/IEEE Design Automation Conference (DAC), 2025. 

## Other Algorithms Developed on Top of CULS
* GPU LUT mapping
* GPU mapping-based AIG optimization
* GPU simulation-based combinational equivalence checking

These algorithms are not open-sourced in CULS due to various reasons, but we can provide
binary executables containing their implementations. To request the executables, 
please send an email to Tianji Liu including your name, affiliation, 
and the intended use of the executable.

## Contributors
* [Shiju Lin](https://shijulin.github.io/): GPU rewriting.
* [Jinwei Liu](https://anticold.github.io/): GPU rewriting.
* [Tianji Liu](https://tefantasy.github.io/): GPU refactoring, balancing,
LUT mapping, mapping-based AIG optimization, GPU simulation-based CEC.
* Yang Sun: GPU resubstitution, mapping-based AIG optimization.
