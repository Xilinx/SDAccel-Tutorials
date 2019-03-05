<table>
 <tr>
   <td align="center"><img src="https://www.xilinx.com/content/dam/xilinx/imgs/press/media-kits/corporate/xilinx-logo.png" width="30%"/><h1>2018.3 SDAccel™ Development Environment Tutorials</h1>
   <a href="https://github.com/Xilinx/SDAccel-Tutorials/branches/all">See other versions</a>
   </td>
 </tr>
 <tr>
 <td align="center"><h3>Using Multiple Compute Units</h3>
 </td>
 </tr>
</table>

## Introduction

This tutorial demonstrates a flexible kernel linking process to increase the number of kernel instances on an FPGA. Each specified instance of a kernel is also known as a compute unit (CU). This process improves the parallelism in a combined host-kernel system.

## Background

By default, the SDAccel™ tool creates one hardware instance (also called a compute unit) for each kernel. A host program can use the same kernel multiple times for different sets of data. In these cases, it is useful to generate multiple compute units of the kernel to let those compute units run concurrently, and improve the performance of the overall system.  

For more information, see _Multiple Instances of a Kernel_ in the _SDAccel Environment Programmers Guide_ ([UG1277](https://www.xilinx.com/support/documentation/sw_manuals/xilinx2018_3/ug1277-sdaccel-programmers-guide.pdf)).

<!-- We should provide some prerequisites for this tutorial, as well as some directions for accessing the lab materials either through cloning the repository or downloading a zip file from xilinx.com.-->

## Description of the Tutorial Example

This tutorial uses an image filter example to demonstrate the multiple compute units feature. The host application processes the image, extracts Y, U, and V planes, and then runs the kernel three times to filter each plane of an image. By default, these three kernels run sequentially, using the same hardware resources because the FPGA only contains a single hardware instance of the kernel. This tutorial demonstrates how to increase the number of compute units called by the host application to filter the Y, U, and V planes _concurrently_, instead.

## Lessons

During this tutorial, you will:
1. Create a new Application project and import source files.
2. Run hardware emulation and inspect the emulation report to identify multiple serial kernel executions.
3. Tweak the host code to enable out-of-order command executions.
4. Alter the kernel linking process to create multiple instances of the same kernel.
5. Re-run hardware emulation, and confirm parallel execution of the compute units.  

## Tutorial Walkthrough

### Setting the Workspace

1. Go to the example directory: `cd using-multiple-cu/reference-files/`
2. Invoke the SDx™ environment GUI: `sdx`
3. Specify a workspace, create a new application project, and then specify the project name as `filter2d`.
4. Select `xilinx_u200_xdma_201830_1` as the platform.
5. Keep **Empty Application** in the **Templates**, and click **Finish**.

The SDx tool will create and open your project in the specified workspace.

### Configuring the design

1. From the Project Explorer window, import host source files from the `src/host` directory, and select all the files.
2. From the Project Explorer window, import the `Filter2DKernel.xo` kernel object file from the `src/kernel` directory.
>**NOTE**: The kernel code is already a compiled object file (.xo) to use in this tutorial. In fact, the `Filter2DKernel.xo` file may be generated from either C/C++ or RTL. They are essentially the same when starting from the compiled object code. You can still customize the linking process starting from the `.xo` file.
3. In the main project window, select **Filter2DKernel** as a hardware function.
4. Specify host code linker option:  
The host code uses the OpenCV™ library for image file operation, so we need to specify related linker options.
  1. In the Project Explorer window, right-click on the top-level folder of the `filter2d` project, and select  **C/C++ Build Settings**.
  2. In the Settings dialog box, select the **SDX GCC Host Linker (x86_64)** tool settings.
  3. At the top of the Settings dialog box, set the **Configuration** drop-down to **All Configuration** so that the linker options apply to all the flows.
  4. In the Expert Settings: Command line pattern field, append the following text to the end of the current string:  
```
  -L${XILINX_SDX}/lnx64/tools/opencv -lopencv_core -lopencv_highgui -Wl,-rpath,${XILINX_SDX}/lnx64/tools/opencv
```  
  5. Select the **Apply and Close** button.  

6. Set runtime arguments:  
From the top Run Menu, set **Program arguments** with `-x ../binary_container_1.xclbin -i ../../../../img/test.bmp -n 1`.

### Run Hardware Emulation

Select **Emulation-HW** for **Active build configuration**, and run the hardware emulation by clicking the **Run** button: (![](./images/RunButton.PNG))

### Inspect the Host Code

While the emulation run is going on, we will take a look at the host code. Open the file by expanding the `src` folder in the **Project Explorer** window, and double-clicking the `host.cpp` file.

Scroll to lines 266-268, where you can see that the Filter function is called three times for Y, U, and V channels, respectively:  
![](./images/host_file1.png)

This function is described from line number 80. Here, you can see kernel arguments are set, and the kernel is executed by the `clEnqueueTask` command:  
![](./images/host_file2.png)

All three `clEnqueueTask` commands are being enqueued using a single in-order command queue (line number 75). So, all the commands using this command queue will be executed sequentially in the order they are added to the queue.  
![](./images/Command_queue.JPG)

### Emulation Result

After the hardware emulation run is finished, click inside the Assistant window (bottom-left). Select **Emulation-HW**_>**filter2d-Default**. From here, you can see a couple of important reports, such as `Profile Summary` and `Application Timeline`.  
![](./images/assistant_2.JPG)

1. Open the **Profile Summary** report by double-clicking on the item in the **Reports** window.  
  * This report provides data related to how the application runs.
  * Note that under **Top Kernel Execution**, the kernel is executed three times.

2. Open the Application Timeline report from the **Emulation-HW** run.
   * The Application Timeline report collects and displays host and device events on a common timeline to help you understand and visualize the overall health and performance of your systems.
   * At the bottom of the timeline, you can see 3 blue bars: one for each kernel enqueing from the host. The host enqueues the kernel execution sequentially (in order) as it is using a single in-order command queue.
   * Below the blue bars, you can see 3 green bars: one for each kernel execution. They are working on the FPGA sequentially.  
   ![](./images/serial_kernel_enqueue.JPG)


### Improving Host Code for Concurrent Kernel Enqueing

Change the host code in line number 75 to declare the command queue as an _out-of-order_ command queue.

Before the change:
```
mQueue   = clCreateCommandQueue(Context, Device, CL_QUEUE_PROFILING_ENABLE, &mErr);
```

After the change:
```
mQueue   = clCreateCommandQueue(Context, Device, CL_QUEUE_PROFILING_ENABLE | CL_QUEUE_OUT_OF_ORDER_EXEC_MODE_ENABLE, &mErr);
```
Save the file by pressing **CTRL**+**S**.  
>**Optional Step:** You can run hardware emulation with the changed host code. If you choose to run the Hardware Emulation feature, use the timeline trace to observe that using the out-of-order queue enables the kernels to be executed at almost the same time as one another.

However, though the host scheduled all these executions concurrently, some of the execution requests are delayed due to the limited kernel instances on the FPGA (the FPGA still executes the kernels sequentially).  
![](./images/sequential_kernels_2.JPG)

In the next step, we will increase the number of kernel instances on the FPGA to allow three host kernel executions concurrently.

Instead of using a single out-of-order queue, we can use multiple in-order queues to achieve the same concurrent command executions from the host code. For more information, refer to [this SDAccel Github Host code example](https://github.com/Xilinx/SDAccel_Examples/blob/master/getting_started/host/concurrent_kernel_execution_ocl/src/host.cpp) that shows both the single out-of-order command queue and multiple in-order command queues approaches.

### Increasing the Number of Kernel Instances

Perform the following steps to increase the number of kernel instances to three:
1. To return to SDx Project Settings, click the **Filter2D** tab.
2. Locate the Hardware Functions section in the bottom half of the window.
3. Increase the number of computer units from `1` to `3`.  
![](./images/SetNumComputeUnits_2.PNG)

### Running Hardware Emulation and inspect the change

1. Run the hardware emulation the same way you previously ran it.
2. After the run has finished, you can review the Profile Summary report.
3. In the Application Timeline window, you can now see that the kernel executions overlap:  
![](./images/overlapping_kernels_2.JPG)

You have learned how to alter the kernel linking process to execute same kernel functions concurrently on an FPGA.

## Optional Step

This tutorial demonstrates the mechanism through hardware emulation. You can change the Active Build Configuration to **System**, and then click the **Run** button to compile and execute this on the actual FPGA board. After this run finishes, you can confirm the timeline trace report from the system run, as well.

<hr/>
<p align="center"><sup>Copyright&copy; 2019 Xilinx</sup></p>
