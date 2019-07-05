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

# Introduction

An SDAccel application consists of a software program running on a host CPU and interacting with one or more accelerators running on a Xilinx FPGA. In this tutorial, you will learn how to write code for the host and for the accelerator, build the application using the SDAccel compiler, and then run and profile the application. The accelerator used in this tutorial is a vector-add function.

The Makefile used in this tutorial has a set of commands to compile, link, and run the host and kernel. The Makefile also provides a capability to enable profiling features to visualize the results.

# Before You Begin

The labs in this tutorial use:

* BASH Linux shell commands.
* 2019.1 SDx release and the *xilinx_u200_xdma_201830_1* platform. If necessary, it can be easily ported to other versions and platforms.

>**IMPORTANT:**  
>
> * Before running any of the examples, make sure you have installed Xilinx Runtime (XRT) and the SDAccel development environment as described in the *SDAccel Development Environment Release Notes, Installation, and Licensing Guide* ([UG1238](https://www.xilinx.com/html_docs/xilinx2019_1/sdaccel_doc/yrc1534452173645.html)).
>* If you run applications on the Alveo card, ensure the card and software drivers have been correctly installed by following the instructions in the *Getting Started with Alveo Data Center Accelerator Cards Guide* ([UG1301](https://www.xilinx.com/support/documentation/boards_and_kits/accelerator-cards/ug1301-getting-started-guide-alveo-accelerator-cards.pdf)).

## Accessing the Tutorial Reference Files

1. To access the reference files, type the following into a terminal: `git clone https://github.com/Xilinx/SDAccel-Tutorials`.
2. Navigate to `SDAccel-Tutorials-master/docs/my-first-sdaccel-application/reference-files`.

# Next Steps

Complete the labs in the following order:

1. [Coding My First C++ Kernel](./cpp_kernel.md): Create the hardware kernel to run on the accelerator card.
2. [Coding My First Host Program](./host_program.md): Create the host program code, including making the required API calls to run the kernel.
3. [Compiling and Linking the Application](./building_application.md): Build the host program and hardware kernel.
4. [Profiling the Application](./profile_debug.md): Profile and optimize the application design.

</br>
<hr/>
<p align= center><b><a href="/README.md">Return to Main Page</a> — <a href="/docs/sdaccel-getting-started/">Return to Getting Started Pathway</a></b></p>
</br>
<p align="center"><sup>Copyright&copy; 2019 Xilinx</sup></p>