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

## Introduction

The following labs will introduce you to the essential concepts for building and running an accelerated application using the SDAccel™ development environment.

## Before You Begin

The labs in this tutorial use:

* BASH Linux shell commands.
* 2019.1 SDx™ release and the *xilinx_u200_xdma_201830_1* platform. If necessary, it can be modified to use other software releases and platforms.

>**IMPORTANT:**  
>
> * Before running any of the examples, make sure you have installed Xilinx Runtime (XRT) and the SDAccel development environment as described in the *SDAccel Development Environment Release Notes, Installation, and Licensing Guide* ([UG1238)](https://www.xilinx.com/html_docs/xilinx2019_1/sdaccel_doc/yrc1534452173645.html).
>* If you run applications on the Alveo™ card, ensure the card and software drivers have been correctly installed by following the instructions in the *Getting Started with Alveo Data Center Accelerator Cards Guide* ([UG1301](https://www.xilinx.com/support/documentation/boards_and_kits/accelerator-cards/ug1301-getting-started-guide-alveo-accelerator-cards.pdf)).

## Accessing the Tutorial Reference Files

1. To access the reference files, type the following into a terminal: `git clone https://github.com/Xilinx/SDAccel-Tutorials`.
2. Navigate to `SDAccel-Tutorials-master/docs/<tutorial name>/reference-files`.

## Next Steps

You should work through the labs in the following order.

1. [Building an Application](./BuildingAnApplication.md): Learn how to build an application's host program and the hardware kernel.
2. [Running Software and Hardware Emulation](./Emulation.md): Run hardware and software emulation on an application.
3. [Generating Profile And Trace Reports](./ProfileAndTraceReports.md): Learn how to generate profiling reports to better understand the performance of the application.
4. [Executing in Hardware](./HardwareExec.md): Finally, execute an application on the Alveo accelerator card.

</br>
<hr/>
<p align= center><b><a href="/README.md">Return to Main Page</a> — <a href="/docs/sdaccel-getting-started/">Return to Getting Started Pathway</a></b></p>
</br>
<p align="center"><sup>Copyright&copy; 2019 Xilinx</sup></p>