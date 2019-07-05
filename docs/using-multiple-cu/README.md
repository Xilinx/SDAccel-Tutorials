<table>
 <tr>
   <td align="center"><img src="https://www.xilinx.com/content/dam/xilinx/imgs/press/media-kits/corporate/xilinx-logo.png" width="30%"/><h1>2019.1 SDAccel™ Development Environment Tutorials</h1>
   <a href="https://github.com/Xilinx/SDAccel-Tutorials/branches/all">See other versions</a>
   </td>
 </tr>
 <tr>
 <td align="center"><h1>Using Multiple Compute Units</h1>
 </td>
 </tr>
</table>

# Introduction

This tutorial demonstrates a flexible kernel linking process to increase the number of kernel instances on an FPGA. Each specified instance of a kernel is also known as a compute unit (CU). This process of increasing number of CU improves the parallelism in a combined host-kernel system.

# Tutorial Overview

By default, the SDAccel™ tool creates one CU for each kernel. A host program can use the same kernel multiple times for different sets of data. In these cases, it is useful to generate multiple CUs of the kernel to let those CUs run concurrently, and improve the performance of the overall system.  

For more information, see [Multiple Instances of a Kernel](https://www.xilinx.com/html_docs/xilinx2019_1/sdaccel_doc/pni1524163195211.html#yzb1524519238289) in the _SDAccel Environment Programmers Guide_ ([UG1277](https://www.xilinx.com/html_docs/xilinx2019_1/sdaccel_doc/vno1533881025717.html)).

During this tutorial, you will:

1. Run hardware emulation and inspect the emulation report to identify multiple serial kernel executions.
2. Change the host code to enable out-of-order command executions.
3. Alter the kernel linking process to create multiple CUs of the same kernel.
4. Re-run the hardware emulation, and confirm the parallel execution of the CUs.

This tutorial uses an image filter example to demonstrate the multiple CU feature. The host application processes the image, extracts Y, U, and V planes, and then runs the kernel three times to filter each plane of an image. By default, these three kernels run sequentially, using the same hardware resources because the FPGA only contains a single CU of the kernel. This tutorial demonstrates how to increase the number of CU, and then executing the kernel runs in parallel.

# Before You Begin

This tutorial uses:

* BASH Linux shell commands
* 2019.1 SDx release and the *xilinx_u200_xdma_201830_1* platform.
If necessary, it can be easily extended to other versions and platforms.

>**IMPORTANT:**
>
>* Before to running any of the examples, make sure you have installed Xilinx Runtime (XRT) and the SDAccel development environment as described in the *SDAccel Development Environment Release Notes, Installation, and Licensing Guide* ([UG1238)](https://www.xilinx.com/html_docs/xilinx2019_1/sdaccel_doc/yrc1534452173645.html).
>* If you run applications on the Alveo™ card, ensure the card and software drivers have been correctly installed by following the instructions in the *Getting Started with Alveo Data Center Accelerator Cards Guide* ([UG1301](https://www.xilinx.com/support/documentation/boards_and_kits/accelerator-cards/ug1301-getting-started-guide-alveo-accelerator-cards.pdf)).

## Accessing the Tutorial Reference Files

1. To access the reference files, enter the following in a terminal: `git clone http://github.com/Xilinx/SDAccel-Tutorials`.
2. Navigate to `SDAccel-Tutorials-master/docs/using-multiple-cu/reference-files`.

# Makefile Flow

You can observe the Makefile used for this tutorial in `using-multiple-cu/reference-files/Makefile`. The top level settings include:

* **XOCC**: XOCC compiler path to compile the kernel code.
* **EMCONFIGUTIL**: The path of the utility file that creates emulation configuration file, `emconfig.json`.
* **DSA**: The target platform.
* **NKERNEL**: The **kernel name:number of CUs** used in the XOCC compiler linker option, `--nk`.
* **KERNEL_XO**: The kernel code is already a compiled object file (XO) to use in this tutorial. In fact, the `Filter2DKernel.xo` file can be generated from either C/C++ or RTL, which are essentially the same when starting from the compiled object code. You can still customize the linking process starting from the XO file.
* **LFLAGS**: Note the linker option using OpenCV™ library for host code linker option.

   ```
  -L${XILINX_SDX}/lnx64/tools/opencv -lopencv_core -lopencv_highgui -Wl,-rpath,${XILINX_SDX}/lnx64/tools/opencv
   ```  

* **EXE_OPT**: The runtime options passed as command line arguments: Compiled kernel xclbin file, input image.

## Run Hardware Emulation

Run hardware emulation with the following command.

   ``` 
   make check MODE=hw_emu
   ```

For hardware emulation (`hw_emu`), the kernel code is compiled into a hardware model, which is run in a
hardware simulator, while the rest of the system uses a C simulator. Building and running takes longer but provides a detailed, cycle-aware, view of kernel activity. This target is useful for testing the functionality of the logic that will run in the FPGA and for getting initial performance estimates.
>**NOTE:** For instructions on how to build the host software and hardware, refer to the [Building an Application](./BuildingAnApplication.md) lab.

## Inspect the Host Code

1. While the emulation run is executing, in another terminal, open the `src/host/host.cpp` file.

2. Inspect lines 255-257. You can see that the Filter function is called three times for the Y, U, and V channels, respectively.

   ```
   request[xx*3+0] = Filter(coeff.data(), y_src.data(), width, height, stride, y_dst.data());
   request[xx*3+1] = Filter(coeff.data(), u_src.data(), width, height, stride, u_dst.data());
   request[xx*3+2] = Filter(coeff.data(), v_src.data(), width, height, stride, v_dst.data());
   ```

   This function is described from line 80. Here, you can see kernel arguments are set, and the kernel is executed by the `clEnqueueTask` command.

   ```
    // Set the kernel arguments
    clSetKernelArg(mKernel, 0, sizeof(cl_mem),       &mSrcBuf[0]);
    clSetKernelArg(mKernel, 1, sizeof(cl_mem),       &mSrcBuf[1]);
    clSetKernelArg(mKernel, 2, sizeof(unsigned int), &width);
    clSetKernelArg(mKernel, 3, sizeof(unsigned int), &height);
    clSetKernelArg(mKernel, 4, sizeof(unsigned int), &stride);
    clSetKernelArg(mKernel, 5, sizeof(cl_mem),       &mDstBuf[0]);

   // Schedule the writing of the inputs
   clEnqueueMigrateMemObjects(mQueue, 2, mSrcBuf, 0, 0, nullptr,  &req->mEvent[0]);

   // Schedule the execution of the kernel
   clEnqueueTask(mQueue, mKernel, 1,  &req->mEvent[0], &req->mEvent[1]);
   ```

   All three `clEnqueueTask` commands are being enqueued using a single in-order command queue (line 75). As a result, all the commands using this command queue are executed sequentially in the order they are added to the queue.

   ```
   Filter2DDispatcher(
           cl_device_id     &Device,
           cl_context       &Context,
           cl_program       &Program )
     {
           mKernel  = clCreateKernel(Program, "Filter2DKernel", &mErr);
           mQueue   = clCreateCommandQueue(Context, Device, CL_QUEUE_PROFI   LING_ENABLE, &mErr);
           mContext = Context;
           mCounter = 0;
     }
   ```

## Emulation Result

1. First, review the generated Profile Summary report (`profile_summary.csv`).

   Convert the Profile Summary report file to HTML format by executing the following command.

   ```
   sdx_analyze profile -i profile_summary.csv -f html
   ```

   * This report provides data related to how the application runs.
   * Note that under "Top Kernel Execution", the kernel is executed three times.

   >**NOTE:** The run directory contains a file named `sdaccel.ini`. This file contains runtime options that generate additional reports such as the Profile Summary report and Timeline Trace. 

2. Next, review the Timeline Trace report (`timeline_trace.csv`). To view the report in the GUI, convert the file to a waveform database (WDB) format.

   ```
   sdx_analyze trace -i timeline_trace.csv
   ```

3. To run the SDx environment GUI, enter the following command.

   ```
   sdx -report timeline_trace.wdb
   ```

   The Application Timeline report collects and displays host and device events on a common timeline to help you understand and visualize the overall health and performance of your systems.
   * At the bottom of the timeline, you can see three blue bars, one for each kernel enqueing from the host. The host enqueues the kernel execution sequentially (in order) as it is using a single in-order command queue.
   * After the blue bars, you can see three green bars, one for each kernel execution. They are working on the FPGA sequentially.
   ![Serial kernel enqueue](./images/serial_kernel_enqueue.JPG)

## Improve the Host Code for Concurrent Kernel Enqueuing

1. Change the `src/host/host.cpp` host file in line 75.  
    This declares the command queue as an _out-of-order_ command queue.  

   Code before the change:

   ```
   mQueue   = clCreateCommandQueue(Context, Device, CL_QUEUE_PROFILING_ENABLE, &mErr);
   ```

   Code after the change:

   ```
   mQueue   = clCreateCommandQueue(Context, Device, CL_QUEUE_PROFILING_ENABLE | CL_QUEUE_OUT_OF_ORDER_EXEC_MODE_ENABLE, &mErr);
   ```

2. (*Optional*) Run the hardware emulation with the changed host code.

   If you choose to run the Hardware Emulation feature, use the Timeline Trace to observe that using the out-of-order queue enables the kernels requested to be executed at almost the same time as one another (the blue bars represent kernel enqueue requests scheduled by the host).  

   However, though the host scheduled all these executions concurrently, second and third execution requests are delayed as there is only one CU on the FPGA (the FPGA still executes the kernels sequentially).  
![Sequential kernels](./images/sequential_kernels_2.JPG)  
In the next step, you will increase the number of CU on the FPGA to allow three host kernel executions concurrently.

## Increasing the Number of CUs

Now, build the kernel xclbin again by altering the link step to generate three CUs of the same kernel.

Open the Makefile, and change the NKERNEL setting. The NKERNEL variable is used in `--nk` switch of XOCC link stage.

```
NKERNEL := Filter2DKernel:3
```

## Run Hardware Emulation and Inspect the Change

1. Regenerate the xclbin file. You need to do `make clean` and `make` to delete the existing xclbin with one CU and to create a new xclbin with three kernel CUs.

   ```
   make clean
   make check MODE=hw_emu
   ```

2. Convert the new `timeline_trace.csv` file to WBD format, and open it in the SDx environment GUI following the same steps as before.

   ```
   sdx_analyze trace -i timeline_trace.csv
   sdx -report timeline_trace.wdb
   ```

   You can now see that the application takes advantage of the three CUs created with the `--nk` switch, and that the kernel executions overlaps and executes in parallel, speeding up the overall application.
![](./images/overlapping_kernels_2.JPG)

# Conclusion

You have learned how to alter the kernel linking process to execute same kernel functions concurrently on an FPGA.

</br>
<hr/>
<p align= center><b><a href="/README.md">Return to Main Page</a></b></p>
<p align="center"><sup>Copyright&copy; 2019 Xilinx</sup></p>
