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

# Getting Started with Alveo

## Introduction

This tutorial directs you to the various resources available to become familiar with the Alveo™ Data Center accelerator card and perform an installation.

For details on installing an Alveo card on your server, visit the [Alveo landing page](http://www.xilinx.com/alveo). It provides links to all the resources necessary to become familiar with the Alveo card along with installation instructions on both RedHat/CentOS and Ubuntu operating systems.

With the Alveo card installed in a server, you can deploy applications to be run on the host server and accelerator card.  However, to develop applications, you will need to install the SDAccel development software.  The SDAccel tools include an optimized compiler for host applications, cross-compiler for the hardware kernel, a robust debugging environment, and profilers to identify performance bottlenecks and optimize the application. The SDAccel development software does not need to be installed on the same server as the Alveo card.  It can be installed on a system without any cards installed.

Alternatively, you can also develop and deploy applications on cloud servers such as [AWS](https://aws.amazon.com/ec2/instance-types/f1/) or [Nimbix](https://www.nimbix.net/alveo/). Both come with everything you need to develop, simulate, debug, and compile your hardware acceleration code.

## Alveo Card Installation

Before installing the Alveo card, watch the [Quick Take Video](https://www.xilinx.com/video/fpga/getting-started-with-alveo-u200-u250.html) for an overview of the hardware and driver software installation, along with a quick navigation of the Alveo landing page.

For Alveo card installation, follow the instructions in the *Getting Started with Alveo Data Center Accelerator Cards User Guide* ([UG1301](https://www.xilinx.com/support/documentation/boards_and_kits/accelerator-cards/ug1301-getting-started-guide-alveo-accelerator-cards.pdf)). It provides the latest hardware and driver software installation procedures and allows you to deploy hardware accelerated applications using the Alveo card. It also includes:

* Minimum system requirements
* List of validated servers
* Overview of the Alveo cards
* Card bring-up and validation steps

For developing application in-house, install the SDAccel environment software, by following the detailed installation procedures found in the *SDAccel Development Environment Release Notes and Installation Guide* ([UG1238](https://www.xilinx.com/html_docs/xilinx2019_1/sdaccel_doc/yrc1534452173645.html)).

For developing applications for AWS, see one of the following tutorials:

* [Getting Started on AWS F1 with SDAccel and C/C++ Kernels](/docs/aws-getting-started/CPP)
* [Getting Started on AWS F1 with SDAccel and RTL Kernels](/docs/aws-getting-started/RTL)

## Next Steps

Understand the [Essential Concepts for Building and Running the Accelerated Application](/docs/Pathway3/) using the SDAccel™ development environment, including building a software application, building a hardware platform, performing emulation, viewing reports, and executing in hardware.

</br>
<hr/>
<p align= center><b><a href="/README.md">Return to Main Page</a> — <a href="/docs/sdaccel-getting-started/">Return to Getting Started Pathway</a></b></p>
</br>
<p align="center"><sup>Copyright&copy; 2019 Xilinx</sup></p>
