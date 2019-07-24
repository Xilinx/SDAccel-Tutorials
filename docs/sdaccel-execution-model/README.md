<table>
 <tr>
   <td align="center"><img src="https://www.xilinx.com/content/dam/xilinx/imgs/press/media-kits/corporate/xilinx-logo.png" width="30%"/><h1>2019.1 SDAccel™ Development Environment Tutorials</h1>
   <a href="https://github.com/Xilinx/SDAccel-Tutorials/branches/all">See other versions</a>
   </td>
 </tr>
 <tr>
 <td align="center"><h1>SDAccel Getting Started Pathway</h1>
 </td>
 </tr>
</table>

# SDAccel Execution Model

## Introduction

An SDAccel™ accelerated application consists of two distinct components: a software program and an FPGA binary containing hardware accelerated kernels. The Xilinx runtime library (XRT) manages control and communication between these two components.

* The software program, written in C/C++ and using API calls like OpenCL™, runs on an x86 server.
* The hardware accelerated kernels run within the Xilinx FPGA on an Alveo™ accelerator card.  

The API calls in the software program, managed by Xilinx runtime (XRT), are used to transfer data between the host and device, execute kernel and ensure proper synchronization. Communication between the host x86 server and the accelerator card occurs across the PCIe bus. Data can be passed directly from the software program to the specified kernels, or it can be placed in global memory which is shared memory space accessible by both the software and the kernels.
 
The general structure of this acceleration platform is shown in the following figure.

![host_fpga_partition_structure](images/host_fpga_partition_structure.png)  

The FPGA hardware platform, on the right-hand side, contains the hardware accelerated kernels, global memory along with the DMA for memory transfers. Kernels can have one or more global memory interfaces and are programmable.  

The SDAccel environment execution model can be broken down into the following steps:  

1. The host application writes the data needed by a kernel into the global memory of the attached device through the PCIe® interface.
2. The host application sets up the kernel with its input parameters.
3. The host application launches the execution of the kernel function on the FPGA.
4. The kernel performs the required computation while reading data from global memory, as necessary.
5. The kernel writes data back to global memory and notifies the host that it has completed its task.
6. The host application can transfer the data from global memory to host memory or can transfer the data to another kernel for processing.

## Next Steps

Install and configure a development system to write and build host programs and hardware kernels using the SDAccel development environment. Install and configure the Alveo accelerator cards in development, or deployment systems.

[Read more...](/docs/alveo-getting-started/)

</br>
<hr/>
<p align= center><b><a href="/README.md">Return to Main Page</a> — <a href="/docs/sdaccel-getting-started/">Return to Getting Started Pathway</a></b></p>
</br>
<p align="center"><sup>Copyright&copy; 2019 Xilinx</sup></p>
