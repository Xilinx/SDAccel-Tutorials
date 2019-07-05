<p align="right">
<a>English</a> | <a href="/docs-jp/README.md">日本語</a>
</p>

<table width="100%">
  <tr width="100%">
    <td align="center"><img src="https://www.xilinx.com/content/dam/xilinx/imgs/press/media-kits/corporate/xilinx-logo.png" width="30%"/><h1>2019.1 SDAccel™ Development Environment Tutorials</h1>
    <a href="https://github.com/Xilinx/SDAccel-Tutorials/branches/all">See other versions</a>
    </td>
 </tr>
 </table>

# Getting Started

## Developing Accelerated Applications using SDAccel Development Tools

![Pathways](images/pathway.png)

### 1. Programming and Execution Model

In the SDAccel™ environment framework, an application is split into a host program and hardware accelerated kernels, with a communication channel between them for data transfer. The host application, written in C/C++ and using API calls like OpenCL, runs on an x86 server; the hardware accelerated kernels run within the Xilinx FPGA on an Alveo accelerator card. [Read more...](/docs/sdaccel-execution-model/)

### 2. Setting up the Alveo Accelerator Cards and SDAccel Tools

Xilinx® Alveo™ Data Center accelerator cards provide compute acceleration performance and flexibility for Data Centers looking to increase throughput. You can install Alveo accelerator cards in deployment systems for running accelerated applications, or in SDAccel development systems, you can develop, debug, and optimize applications running on Alveo accelerator cards. [Read more...](/docs/alveo-getting-started/)

### 3. Building the Accelerated Application - Essential Concepts

The following labs will introduce you to the essential concepts for building and running an accelerated application using the SDAccel development environment.

In this tutorial, you will learn how to do the following:

- Build an application's host software and the hardware platform.
- Run hardware and software emulation on an application.
- Learn how to generate application profiling reports to better understand an application's performance.  
- Execute an application on an accelerator card.

[Read more...](/docs/Pathway3/)

### 4. Your First Program

After learning the essential concepts, you will work through the steps of creating a simple SDAccel application by building the x86 host program and kernel code (compute device), and use a Makefile to build the design. The SDAccel environment consists of a host x86 CPU and hardware kernel running on a Xilinx FPGA on an Alveo accelerator card. The kernel code in this example performs a simple vector addition, `C[i]= A[i]+ B[i]`. This tutorial also demonstrates the host code structure and required API calls in detail.

In this tutorial, you will learn how to do the following:

- Create a simple hardware kernel code to run on the accelerator card.
- Create a simple host program code, including making the required API calls to run the kernel.
- Build the host program and hardware kernel.
- Profile and optimize the application design.

[Read more...](/docs/my-first-sdaccel-application/)

### 5. Optimizing Accelerated FPGA Applications - Based on SDAccel Methodology

The methodology for developing optimized accelerated applications is comprised of two major phases: architecting the application, and developing the hardware kernels. In the first phase, you make key decisions about the application architecture by determining which software functions should be accelerated onto FPGA kernels, how much parallelism can be achieved, and how to deliver it in code. In the second phase, you implement the kernels by structuring the source code, and applying the necessary compiler options and pragmas to create the kernel architecture needed to achieve the optimized performance target.

In this tutorial, you will learn how to do the following:

- Create an SDAccel application from the C application.
- Optimize memory transfers.
- Optimize using fixed point data types.
- Optimize with dataflow.
- Use out-of-order queues and multiple compute units.
- Run the accelerator in hardware.

[Read more...](/docs/convolution-tutorial/)

### Additional Reading

* _SDAccel Methodology Guide_ ([UG1346](https://www.xilinx.com/support/documentation/sw_manuals/xilinx2019_1/ug1346-sdaccel-methodology-guide.pdf))

</br>
<hr/>
<p align="center"><b><a href="/README.md">Return to Main Page</a></b></p>

<p align="center"><sup>Copyright&copy; 2019 Xilinx</sup></p>
