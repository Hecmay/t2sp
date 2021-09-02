T2SP (Temporal To Spatial Programming, previously called T2S) enables software programmers to build a high-performance design on a spatial architecture (like FPGAs) in a constructive, incremental, and productive way. Particularly, programmers can quickly build sophisticated systolic arrays on spatial architectures, where systolic arrays are often the key to achieve high performance. This methodology extends [Halide](https://halide-lang.org/) from temporal architectures (CPUs and GPUs) to spatial architectures.

T2SP is available under a permissive license, the [BSD+Patent license](./LICENSE.md).

In this initial release, we support only Intel FPGAs. We assume your FPGA is local to you, or within Intel FPGA DevCloud, and the operating system is Linux. Other platforms might also work, but are not tested. 

# [DevCloud] Open an account

 + Register at [DevCloud](https://software.intel.com/content/www/us/en/develop/tools/devcloud/fpga.html). 
 + Follow the instructions of an approval email to set up your connection to DevCloud.
 + Add the following to your .bashrc:

```
    if [ -f /data/intel_fpga/devcloudLoginToolSetup.sh ]; then
        source /data/intel_fpga/devcloudLoginToolSetup.sh
    fi
```

# Open an interactive terminal

Local: open a bash shell

DevCloud: 

+ log into a compute node:

```
    devcloud_login
```

+ Choose 

```
    6) Enter Specific Node Number
       Choose a node with Arria 10 Release 1.2.1, or with Stratix 10.
```

# Clone T2SP

```
    git clone https://github.com/IntelLabs/t2sp
```

# Install tools

Local FPGA: Install the Intel FPGA SDK for OpenCL, 19.1 or above

+ Download [Intel FPGA SDK for OpenCL](http://dl.altera.com/opencl/)

+ Install

```
    tar -xvf AOCL-pro-*-linux.tar 
    ./setup_pro.sh
```

All platforms: we assume you have python >= 2.7 already installed. Install llvm-clang >= 9.0, gcc >= 7.5.0, and python's numpy and matplotlib package, if not yet:

```
    cd $HOME/t2sp
    ./install-tools.sh gcc             # if not installed yet
    ./install-tools.sh llvm-clang      # if not installed yet
    ./install-tools.sh python-packages # if not installed yet
````

During installation of gcc and llvm-clang, other dependencies might be needed. Here are some dependencies we know, and you can install them if needed:

```
    ./install-tools.sh m4|gmp|mpfr|mpc|cmake
````

Or you can install all the above tools without giving any option:

```
    ./install-tools.sh
```
This may take ~1 hour on a DevCloud machine.

# Set up environment

```
    cd $HOME/t2sp
    cp setenv.sh my-setenv.sh
```

If you have your own gcc, llvm or clang and thus did not use `install-tools.sh` as shown above, in `my-setenv.sh`, modify the following path variables appropriately:

```
    export GCC_PATH=...
    export LLVM_CONFIG=...
    export CLANG=...
```

If you installed the Intel FPGA SDK for OpenCL for your local FPGA, check the following variables, and modify if needed:

```
    ALTERA_PATH=...
    AOCL_VERSION=...
    FPGA_BOARD_PACKAGE=...
    export FPGA_BOARD=...
    export LM_LICENSE_FILE=...
```

Note: here is an example to find out the board package and board (Assume Intel FPGA SDK for OpenCL 19.1 was installed under directory $HOME/intelFPGA_pro):

```
    $HOME/intelFPGA_pro/19.1/hld/bin/aoc -list-boards
    Board list:
      a10gx
         Board Package: $HOME/intelFPGA_pro/19.1/hld/board/a10_ref
    
      a10gx_hostpipe
         Board Package: $HOME/intelFPGA_pro/19.1/hld/board/a10_ref
```
There are 1 board package and 2 boards in this case, and you should set `FPGA_BOARD_PACKAGE=a10_ref`, and either `export FPGA_BOARD=a10gx` or `export FPGA_BOARD=a10gx_hostpipe`.

Set up the environment (whenever a new terminal is open) with one of the following commands:

```
    source ./my-setenv.sh local        # If using a local FPGA
    source ./my-setenv.sh devcloud     # If using DevCloud
```

# Build T2SP (whenever you change the source code)

```
    cd Halide
    make -j
    cd -
```

+ For debugging the T2SP compiler with source code information, ```make -j OPTIMIZE="-O0 -g"``` instead.
+ For the T2SP compiler to throw exceptions instead of silently exit in case of any error, ```make -j WITH_EXCEPTIONS=1``` instead.
+ To debug runtime, ```make -j OPTIMIZE_RUNTIME="-O0 -g" ``` instead.
+ To enable all of them, ```make -j OPTIMIZE="-O0 -g" WITH_EXCEPTIONS=1 OPTIMIZE_RUNTIME="-O0 -g" ``` instead.

# Regression tests

```
     cd t2s/tests/correctness
    ./test.sh
```
Each sub-directory there contains a success.txt and failure.txt, which have the command lines for compiling and running every test. These tests are small examples one can play with.

To remove all the temporary files generated during the regression testing:

```
    ./test.sh clean
```

# Tutorials

We have a set of [tutorials](https://github.com/intel/FPGA-Devcloud/tree/master/main/QuickStartGuides/T2S) at Intel FPGA DevCloud. A compiler binary is also there, and all dependencies have been installed, so you may start using the programming environment immediately.

# Next release

+ A new language interface for productive and portable performance across FPGAs and GPUs, and a performance test suite. This work enables programmers to build a hardware systolic array on an FPGA and a software systolic array on a GPU with portably high performance in a single specification. Aim to open in November, 2021.

# Citation

If you use T2SP, please cite the following position paper:

```
    @article{T2SP,
      author    = {Hongbo Rong},
      title     = {Programmatic Control of a Compiler for Generating High-performance Spatial Hardware},
      journal   = {CoRR},
      volume    = {abs/1711.07606},
      year      = {2017},
      url       = {http://arxiv.org/abs/1711.07606},
      archivePrefix = {arXiv},
      eprint    = {1711.07606},
      timestamp = {Mon, 13 Aug 2018 16:46:47 +0200},
      biburl    = {https://dblp.org/rec/journals/corr/abs-1711-07606.bib},
      bibsource = {dblp computer science bibliography, https://dblp.org},
      note      = {Open source available at https://github.com/IntelLabs/t2sp}
    }
```

# Publications

+ **SuSy: a programming model for productive construction of high-performance systolic arrays on FPGAs**. 
Yi-Hsiang Lai, Hongbo Rong, Size Zheng, Weihao Zhang, Xiuping Cui, Yunshan Jia, Jie Wang, Brendan Sullivan, Zhiru Zhang, Yun Liang, Youhui Zhang, Jason Cong, Nithin George, Jose Alvarez, Christopher Hughes, and Pradeep Dubey. 2020.  ICCAD'20. [Link](https://ieeexplore.ieee.org/document/9256583) 

+ **T2S-Tensor: Productively Generating High-Performance Spatial Hardware for Dense Tensor Computations**. 
Nitish Srivastava, Hongbo Rong, Prithayan Barua, Guanyu Feng, Huanqi Cao, Zhiru Zhang, David Albonesi,Vivek Sarkar, Wenguang Chen, Paul Petersen, Geoff Lowney, Adam Herr, Christopher Hughes,Timothy Mattson, Pradeep Dubey. FCCM, 2019. [Link](https://ieeexplore.ieee.org/document/8735529)

# [Acknowledgement](./ACKNOWLEDGEMENT.md)