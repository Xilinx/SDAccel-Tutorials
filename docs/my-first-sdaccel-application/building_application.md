<table>
 <tr>
   <td align="center"><img src="https://www.xilinx.com/content/dam/xilinx/imgs/press/media-kits/corporate/xilinx-logo.png" width="30%"/><h1>2019.1 SDAccel™ Development Environment Tutorials</h1>
   <a href="https://github.com/Xilinx/SDAccel-Tutorials/branches/all">See other versions</a>
   </td>
 </tr>
 <tr>
 <td align="center"><h1>Create My First SDAccel Program</h1>
 </td>
 </tr>
</table>

# 3. Compiling, Linking, and Running the Application

## Introduction

An SDAccel application is an heterogeneous application with two distinct components: the software program and the FPGA binary. These two components are built using dedicated compilation chains. This lab describes how to compile, link, and run the VADD example from the command line.

## Building the Application

### Building the Host Program

The host application, written in C/C++ using OpenCL™ API calls and can be built using either the standard GCC compiler or XCPP, a simple wrapper around the GCC. Each source file is compiled to an object file (O), and linked with the Xilinx SDAccel runtime shared library to create the executable (EXE). For details on GCC and its associated command line options, refer to [Using the GNU Compiler Collection (GCC)](https://gcc.gnu.org/onlinedocs/gcc/).

To build the host code, use the following XCPP command for the `./reference-files/src` files.

   ```bash
xcpp -I$XILINX_XRT/include/ -I$XILINX_VIVADO/include/ -Wall -O0 -g -std=c++14 ./src/host.cpp  -o 'host'  -L$XILINX_XRT/lib/ -lOpenCL -lpthread -lrt -lstdc++
   ```

This command specifies the required libraries and include files for Xilinx Runtime (XRT), the Vivado tools, and the OpenCL API. For more information on building the host, refer to the [Building an Application](https://github.com/Xilinx/TechDocs/blob/SDAccel-Tutorials-XDF-develop/docs/Pathway3/BuildingAnApplication.md) lab in the  Essential Concepts for Building and Running the Accelerated Application tutorial.

>**TIP**: To run the commands shown here, you must set up the environment for the SDX development tools and XRT as discussed in *Building an Application*.

### Building the FPGA Binary

Similar to building the software application, building the hardware requires [compiling](../Pathway3/BuildingAnApplication.md#hardware-compilation) and [linking](../Pathway3/BuildingAnApplication.md#hardware-linking) steps using the XOCC compiler.

In the following command, the build target is for software emulation (`-t sw_emu`). You can also specify other build targets, such as `hw_emu` and `hw` as explained in the [Building an Application](https://github.com/Xilinx/TechDocs/blob/SDAccel-Tutorials-XDF-develop/docs/Pathway3/BuildingAnApplication.md) lab in the Essential Concepts for Building and Running the Accelerated Application tutorial.

* Use the following commands for compiling the hardware kernel for the VADD example.

   ```bash
   xocc -t sw_emu --platform xilinx_u200_xdma_201830_1 -g -c -k vadd -I'../src' -o'vadd.xilinx_u200_xdma_201830_1.xo' './src/vadd.cpp'
   ```

* Use the following commands for linking the hardware kernel for the VADD example.

   ```bash
   xocc -t sw_emu --platform xilinx_u200_xdma_201830_1 -g -l --nk vadd:1:vadd_1 -o'vadd.xilinx_u200_xdma_201830_1.xclbin' vadd.xilinx_u200_xdma_201830_1.xo
   ```

## Running Emulation

Before running the design on hardware, run the design in [Running Software and Hardware Emulation](../Pathway3/Emulation.md) mode to verify functionality. Running the application in any emulation mode is a two-step process:

1. Generating an emulation configuration file.
2. Running the application.

### Software Emulation

1. To run in software emulation mode(`-t sw_emu`), generate an emulation configuration file, and set the XCL_EMULATION_MODE environment variable.

   ```bash
   emconfigutil --platform xilinx_u200_xdma_201830_1
   export XCL_EMULATION_MODE=sw_emu  
   ```

2. With the configuration file `emconfig.json` generated and XCL_EMULATION_MODE set, use the following command to execute the host program and kernel in software emulation mode.

   ```bash
   ./host vadd.xilinx_u200_xdma_201830_1.xclbin
   ```

3. Confirm the emulation run.

   ```
   Found Platform
   Platform Name: Xilinx
   INFO: Reading vadd.xilinx_u200_xdma_201830_1.xclbin
   Loading: 'vadd.xilinx_u200_xdma_201830_1.xclbin'
   TEST PASSED
   ```

>**IMPORTANT**: For RTL kernels, software emulation can be supported if a C-model is associated with the kernel. When a C-model is not available, hardware emulation must be used to debug the kernel code.

### Hardware Emulation

1. For hardware emulation, rebuild the kernels using the `-t hw_emu` XOCC option.

   ```bash
   xocc -t hw_emu --platform xilinx_u200_xdma_201830_1 -g -c -k vadd -I'../src' -o'vadd.xilinx_u200_xdma_201830_1.xo' './src/vadd.cpp'
   xocc -t hw_emu --platform xilinx_u200_xdma_201830_1 -g -l --nk vadd:1:vadd_1 -o'vadd.xilinx_u200_xdma_201830_1.xclbin' vadd.xilinx_u200_xdma_201830_1.xo
   ```

2. To run in Hardware emulation mode, generate an emulation configuration file, and set the XCL_EMULATION_MODE environment variable.

   ```bash
   emconfigutil --platform xilinx_u200_xdma_201830_1
   export XCL_EMULATION_MODE=hw_emu
   ```   

3. Use the following command to execute the host program and kernel in hardware emulation mode.

   ```
   ./host vadd.xilinx_u200_xdma_201830_1.xclbin
   ```

4. Confirm the emulation run.

   ```
   Found Platform
   Platform Name: Xilinx
   INFO: Reading vadd.xilinx_u200_xdma_201830_1.xclbin
   Loading: 'vadd.xilinx_u200_xdma_201830_1.xclbin'
   INFO: [HW-EM 01] Hardware emulation runs simulation underneath. Using a large data set will result in long simulation times. It is recommended that a small dataset is used for faster execution. The flow uses approximate models for DDR memory and interconnect and hence the performance data generated is approximate.
   TEST PASSED
   INFO: [SDx-EM 22] [Wall clock time: 16:39, Emulation time: 0.108767 ms] Data transfer between kernel(s) and global memory(s)
   vadd_1:m_axi_gmem-DDR[1]          RD = 32.000 KB              WR = 16.000 KB
   ```

## Running on Hardware

After the functional correctness and host-kernel integration has been verified using the emulation modes, now target (`-t hw`) the Alveo accelerator card on the Xilinx FPGA.

### Hardware Execution

1. For hardware, rebuild the kernels using the `-t hw` XOCC option.

   ```bash
   xocc -t hw_emu --platform xilinx_u200_xdma_201830_1 -g -c -k vadd -I'../src' -o'vadd.xilinx_u200_xdma_201830_1.xo' './src/vadd.cpp'
   xocc -t hw_emu --platform xilinx_u200_xdma_201830_1 -g -l --nk vadd:1:vadd_1 -o'vadd.xilinx_u200_xdma_201830_1.xclbin' vadd.xilinx_u200_xdma_201830_1.xo
   ```

2. Ensure the environment variable XCL_EMULATION_MODE is not set.

   ```
   unset XCL_EMULATION_MODE
   ```

   The hardware build can take a while because the design now needs to be synthesized in the Vivado tool and packaged into a bitstream that is loaded on the FPGA hardware.

3. Once the build is ready and the environment is correctly set, you execute the design to run on the Alveo accelerator card.

   ```
   ./host vadd.xilinx_u200_xdma_201830_1.xclbin
   ```

4. Confirm the emulation run.

   ```
   Found Platform
   Platform Name: Xilinx
   INFO: Reading vadd.xilinx_u200_xdma_201830_1.xclbin
   Loading: 'vadd.xilinx_u200_xdma_201830_1.xclbin'
   TEST PASSED
   ```

## Using the Makefile

The Makefile is a simple way to organize the commands and options, and makes it easier to repeat the builds as needed. The `Makefile`, provided in `./reference-files`, addresses the commands defined above, with additional structure to help you clean up builds, and rebuild the project.

The `Makefile` provided requires you to specify the build TARGET, the hardware DEVICE, and the version (VER) of the host code you are using.

* To target software emulation, use the following `make` command.

   ```
    make check TARGET=sw_emu DEVICE=<DEVICE> VER=host_cpp
    ```

* To target hardware emulation, use the following `make` command.

   ``` 
   make check TARGET=hw_emu DEVICE=<DEVICE> VER=host_cpp
   ```

* To target hardware, use the following `make` command.

   ```
   make check TARGET=hw DEVICE=<DEVICE> VER=host_cpp
   ```

## Next Step

The next step in this tutorial is to [profile the application](./profile_debug.md).

</br>
<hr/>
<p align="center"><b><a href="/docs/sdaccel-getting-started/">Return to Getting Started Pathway</a> — <a href="./README.md">Return to Start of Tutorial</a></b></p>

<p align="center"><sup>Copyright&copy; 2019 Xilinx</sup></p>