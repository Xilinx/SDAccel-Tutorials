# Practice and Experiment with RTL Kernels

Apply and practice what you've learned in the earlier steps of this guide by running more hands-on examples. Familiarize yourself with online resources relating to the SDAccel™ environment, and make your way to the [AWS forum](https://forums.aws.amazon.com/forum.jspa?forumID=243) to search for knowledge and find answers.

## Experiment with Other Examples

Running and experimenting with the following three examples of RTL kernels will help you further familiarize yourself with the RTL kernel flow.

#### Example 1: Vector Addition with two Clocks

This example shows vector addition performed by an RTL kernel with two clocks and the use of the `--kernel_frequency` XOCC option.

Download and run [this example](https://github.com/Xilinx/SDAccel_Examples/tree/master/getting_started/rtl_kernel/rtl_vadd_2clks) from the SDAccel GitHub repository.


#### Example 2: Vector Addition with two Kernels

This example shows how create an accelerated design using more than one RTL Kernel. In this example, Vector Addition is performed with two kernels (Kernel_0 and Kernel_1) which perform vector addition. Kernel_1 reads the output from Kernel_0 as one of two inputs.

Download and run [this example](https://github.com/Xilinx/SDAccel_Examples/tree/master/getting_started/rtl_kernel/rtl_vadd_2clks) from the SDAccel GitHub repository.

#### Example 3: Streaming Connections between multiple RTL Kernels

This example shows how to create streaming connections between three RTL kernels. The input stage kernel reads data from global memory and streams it to the adder kernel using a kernel-to-kernel AXI-Stream connection. The output of the adder kernel is streamed out to the output stage kernel which then writes the results into global memory.

Download and run [this example](https://github.com/Xilinx/SDAccel_Examples/tree/master/getting_started/rtl_kernel/rtl_adder_streams) from the SDAccel GitHub repository.


## Get Support and Troubleshoot Issues

The [AWS F1 SDAccel Development forum](https://forums.aws.amazon.com/forum.jspa?forumID=243) is the place to look for answers, share knowledge and get support. Make sure to subscribe to the forum by clicking the **Watch Forum** link in the Available Actions section.



## Learn More about the SDAccel Environment

#### Documentation for SDAccel v2019.1
* _SDx Development Environment Release Notes, Installation, and Licensing Guide_ ([UG1238](https://www.xilinx.com/html_docs/xilinx2019_1/sdaccel_doc/gsv1547661552998.html#gsv1547661552998))
* _SDAccel Programmers Guide_ ([UG127](https://www.xilinx.com/html_docs/xilinx2019_1/sdaccel_doc/vno1533881025717.html))
* _SDAccel Environment User Guide_ ([UG1023](https://www.xilinx.com/html_docs/xilinx2019_1/sdaccel_doc/itd1534452174535.html))
* _SDAccel Environment Optimization Guide_ ([UG1207](https://www.xilinx.com/html_docs/xilinx2019_1/sdaccel_doc/itd1534452174535.html))

<hr/>
<p align="center"><b>
<a href="STEP5.md">NEXT: Install and Run SDAccel on your own Machine</a>
</b></p>
<br>
<hr/>
<p align="center"><sup>Copyright&copy; 2019 Xilinx</sup></p>
