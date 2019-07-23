<table>
 <tr>
   <td align="center"><img src="https://www.xilinx.com/content/dam/xilinx/imgs/press/media-kits/corporate/xilinx-logo.png" width="30%"/><h1>2019.1 SDAccel™ Development Environment Tutorials</h1>
   <a href="https://github.com/Xilinx/SDAccel-Tutorials/branches/all">See other versions</a>
   </td>
 </tr>
 <tr>
 <td align="center"><h1>Essential Concepts for Building and Running the Accelerated Application</h1>
 </td>
 </tr>
</table>

# 4. Executing in Hardware

>**IMPORTANT:** This lab requires the use of an Alveo accelerator card to run the compiled kernel binary, xclbin. If you do not have access to a machine with an Alveo card installed, you will not be able to complete this lab.

## Introduction

In hardware execution, the host program runs on the host machine, and the kernel code runs on the Alveo accelerator card on a Xilinx device. The performance data you get from hardware execution is the actual performance. The Profile Summary and Timeline Trace reports described in the previous lab are also available.

>**TIP:** Turning on device profiling for the system build (`-t hw`) is intrusive and can negatively affect overall application performance. This feature should only be used for performance debugging, and should be disabled in the production build.

## Before You Begin

Review and run the [Building an Application](./BuildingAnApplication.md), [Running Software and Hardware Emulation](./Emulation.md) and [Generating Profile and Timeline Trace Reports](./ProfileAndTraceReports.md) labs before running this lab. This lab uses the source files and options as in these previous labs, though all the necessary commands to build the host and hardware platform will be shown in this lab.

## Building the Design

Before running applications in Xilinx boards or devices, it is necessary to build the design with the desired system mode, which is specified through the `-t hw` xocc option when building the hardware.

The [Building an Application](./BuildingAnApplication.md) lab describes building the host software and hardware and how to specify the *<build_target>*.

>**IMPORTANT:** Before running any of the examples, ensure you have sourced the runtime script.
>
>  ```bash
>  #Setup runtime. XILINX_XRT will be set in this step
>  #Note that it's only necessary to setup runtime before deployment on hardware.
>  source /opt/xilinx/xrt/setup.sh
>  ```
>
>**TIP**: Also, ensure the environment variable XCL_EMULATION_MODE is not set. In previous labs, it might have been set to `sw_emu` or `hw_emu`.

Run the following command.

   ```bash
   unset XCL_EMULATION_MODE
   ```

## Running the Application

With the host program and hardware platform (xclbin), you can run the application on Xilinx boards or devices. If you have not built the host program and hardware platform, you can use the following commands.

>**IMPORTANT**: Due to the synthesis and implementation of the FPGA binary file, compiling and linking for the hardware target can take significant time.

  ```bash
  source /opt/Xilinx/SDx/2019.1/settings64.sh
  source /opt/xilinx/xrt/setup.sh
  #make sure working path is in reference-files/run
  xcpp -I$XILINX_XRT/include/ -I$XILINX_VIVADO/include/ -Wall -O0 -g -std=c++14 ../src/host.cpp  -o 'host'  -L$XILINX_XRT/lib/ -lOpenCL -lpthread -lrt -lstdc++
  xocc -t hw --platform xilinx_u200_xdma_201830_1 -c -k mmult -I'../src' -o'mmult.hw.xilinx_u200_xdma_201830_1.xo' '../src/mmult.cpp'
  xocc -t hw --platform xilinx_u200_xdma_201830_1 -l --nk mmult:1:mmult_1 -o'mmult.hw.xilinx_u200_xdma_201830_1.xclbin' mmult.hw.xilinx_u200_xdma_201830_1.xo
  ```

***Running the application***

  ```bash
  ./host mmult.hw.xilinx_u200_xdma_201830_1.xclbin
  ```

After successfully running the application, you will see a similar output in the Console:

  ```
  Loading: 'mmult.hw.xilinx_u200_xdma_201830_1.xclbin'
  TEST PASSED
  ```

## Using xbutil to Check Board Status and Debug

Using `xbutil` is optional, but it is a convenient tool to do analysis and debug related to Xilinx boards. For example, run the following command to check board status.

  ```bash
  xbutil query
  ```

The board status, compute unit status, and so on display. For more information about `xbutil`, refer to the *SDx Command and Utility Reference Guide* ([UG1279](https://www.xilinx.com/html_docs/xilinx2019_1/sdaccel_doc/ckx1534452174973.html)) and *SDAccel Debugging Guide* ([UG1281](https://www.xilinx.com/html_docs/xilinx2019_1/sdaccel_doc/caz1534452170629.html)).

## Next Steps

You should have an understanding of the steps required for building and running an application in the SDAccel development environment. These include coding the software program and kernel, building the application, running software and hardware emulation, viewing reports, and running the application on an accelerator card. The next steps will be to use this understanding to [build your first program](/docs/my-first-sdaccel-application).

</br>
<hr/>
<p align="center"><b><a href="/docs/sdaccel-getting-started/">Return to Getting Started Pathway</a> — <a href="./README.md">Return to Start of Tutorial</a></b></p>

<p align="center"><sup>Copyright&copy; 2019 Xilinx</sup></p>
