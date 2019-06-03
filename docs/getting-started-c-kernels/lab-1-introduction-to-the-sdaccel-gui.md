<table style="width:100%">
  <tr>
    <td  align="center" width="100%" colspan="6"><img src="https://www.xilinx.com/content/dam/xilinx/imgs/press/media-kits/corporate/xilinx-logo.png" width="30%"/><h1>2018.3 SDAccel™ Development Environment Tutorials</h1>
    <a href="https://github.com/Xilinx/SDAccel-Tutorials/branches/all">See other versions</a>
</td>
  </tr>
  <tr>
    <td colspan="3" align="center"><h1>Getting Started with C/C++ Kernels</h1></td>
  </tr>  <tr>
    <td align="center"><a href="README.md">Introduction</td>
    <td align="center">Lab 1: Introduction to the SDAccel Development Environment</td>
    <td align="center"><a href="lab-2-introduction-to-the-sdaccel-makefile.md">Lab 2: Introduction to the SDAccel Makefile</a></td>
  </tr>
</table>

# Lab 1: Introduction to the SDAccel Development Environment

This lab demonstrates two different flows: steps 1-3 explain the GUI flow, and step 4 explains the Makefile flow.

This lab uses an example from the Xilinx&reg; SDAccel&trade; Example GitHub repository, which can be found [here](https://github.com/Xilinx/SDAccel_Examples).

## Step 1: Create an SDAccel Project From a GitHub Example

1. Use the `sdx` command to launch the SDx development environment in a terminal window in Linux.

   The Workspace Launcher dialog box is displayed.

2. Select a location for your workspace, which is where the project will be located.

   ![error: missing image](./images/183_launcher.png)

3. Click **Launch**.

   The Welcome window opens.

   >**NOTE**: The Welcome window opens when you use the tool for the first time, or by selecting **Help > Welcome**.

4. In the Welcome window, click **Create Application Project**.

   ![SDxIDEWelcome.png](images/SDxIDEWelcome.png)

   The New SDx Application Project dialog box opens.

5. Create the project.
   1. In Project Name, enter `helloworld`.
   2. Select **Use default location**.
   3. Click **Next**.  

   ![error: missing image](./images/183_application_project.png)

   The Platform page opens.

6. Select `xilinx_u200_xdma_201830_1`, and then click **Next**.

   ![error: missing image](./images/183_hardware_platform_dialog.png)

   The Templates page opens, showing templates you can use to start building an SDAccel environment project. Unless you have downloaded other SDx examples, you should only see _Empty Application_ and _Vector Addition_ as available templates.

   >**NOTE**: The type of hardware platform you select determines the project as an SDAccel or SDSoC™ environment project.

7. In this lab, you will use the Helloworld example from the GitHub repository. To download the examples, click **SDx Examples**.

   ![error: missing image](./images/183_example_empty.png)

8. Select **SDAccel Examples**, and then click **Download**.

   ![error: missing image](./images/183_example_download.png)

   The system clones the GitHub repository to the designated location. When the download completes, the SDAccel Examples tree table is populated and expanded.

   >**NOTE**: The download can take a long time, depending on your connectivity speed. The Progress Information dialog box is displayed until the repository cloning is completed.

9. Click **OK**. This closes the window and brings you back to the Templates window.

   ![error: missing image](./images/183_example_full.png)

   The Templates window is now populated with the SDAccel GitHub examples.  

10. In **Find**, enter `hello`, and then locate the Hello World (HLS C/C++ Kernel) from the Host Examples.

11. Click **Finish**.

    ![error: missing image](./images/183_github_example_new.png)

   The _helloworld_ project is created and displayed in the SDAccel environment, which looks like the following figure.

   ![error: missing image](./images/183_helloworld_project.png)

For more information on the features of the SDx IDE, refer to the _SDAccel Environment User Guide_ ([UG1023](https://www.xilinx.com/cgi-bin/docs/rdoc?v=2018.3;d=ug1023-sdaccel-user-guide.pdf)).

## Step 2: Run Software Emulation

This step shows you how to run software emulation for a design by doing the following:

* Setting the Run Configuration settings
* Opening reports
* Launching Debug

For details about reports and Debug, refer to the _SDAccel Environment User Guide_ ([UG1023](https://www.xilinx.com/cgi-bin/docs/rdoc?v=2018.3;d=ug1023-sdaccel-user-guide.pdf)).

1. Go to Application Project Settings, and in the top-right corner, set **Active build configuration** to **Emulation-SW**.

   ![error: missing image](./images/183_project_settings_sw.png)

   The GitHub example includes an accelerator for the design, so you do not need to add a hardware function.

   >**NOTE**: To add a hardware functions to a design, click ![error: missing image](./images/qpg1517374817485.png). This analyzes the C/C++ code and determines functions that can be used for acceleration.

2. Click ![[the Run Button]](./images/lvl1517357172451.png) (**Run**).

   This builds the project before running the emulation.

   >**NOTE**: The build and emulation process can take a few minutes or longer to complete. During that, open the Run Configurations dialog box to see how you can add specific command line options to customize your build.

3. Go to the Run menu, and then select **Run Configurations**.

   Under the Arguments tab, in the Program Arguments field, you can add Xilinx OpenCL&trade; Compiler (XOCC) command line flags and switches. For a description of command options, refer to the _SDx Command and Utility Reference Guide_ ([UG1279](https://www.xilinx.com/cgi-bin/docs/rdoc?v=2018.3;d=ug1279-sdx-command-utility-reference-guide.pdf)). In this tutorial, no command line arguments are needed for the design to function.

   In the Profile tab, there is a drop-down menu for Generate timeline trace report. You can click the options to see what types of reports are generated. There is also a check box for **Enable Profiling** in this tab.

4. Close the window without changing anything.

   >**NOTE**: If you make changes in the Run Configurations dialog box, click **Run** to re-run the current emulation step and see the changes you made.

   The Console window now displays `TEST PASSED`.

5. After the emulation run completes, you can review the Profile Summary and Application Timeline reports for details on further optimizations. In the Assistant window, double-click **Profile Summary**, as shown below.

   ![error: missing image](./images/183_assistant_reports_sw.png)

   Here, you can view operations, execution time, bandwidth, and other useful data that you can use to optimize the design.

   > **NOTE**: Your summary numbers might vary from the following figure.

   ![error: missing image](./images/183_profile_summary_sw.png)

6. To view the Application Timeline report, in the Assistant window, double-click **Application Timeline**.

   This shows a breakdown of the host code and the kernel code, and execution time for each. To zoom in to a specific area, click and drag the mouse to the right.

   ![error: missing image](./images/183_application_timeline.png)  

   The Profile Summary and Application Timeline present data on how the host code and kernel communicate and process kernel information. You can use the Debug feature to go through host-kernel processing and identify issues.

7. In the Project Explorer window, to open the file in the editor, double-click **host.cpp** (located in the **Explorer > src` directory**).

8. Before you can run in Debug, you must set a breakpoint. Setting breakpoints at key points in the execution helps to identify problems. To pause the host code prior to kernel debug, right-click <`line 89`> in the blue area (see figure below) on the (`OCL_CHECK(err, err = q.enqueueMigrateMemObjects({buffer_in1, buffer_in2},0/* 0 means from host*/));`), and select **Toggle Breakpoint**.

   ![error: missing image](./images/debug_breakpoint_hw.png)

9. To run Debug, click ![missing image: the Debug icon](./images/cwo1517357172495.png).

   A dialog box opens and displays options to switch perspectives.

10. Click **Yes**.

    By default, the debugger inserts an automatic breakpoint at the first line of `main`. On the Debugger tab of the Runs Configuration dialog, there is an option to stop on the `main` function, which is enabled by default (as shown below). This is helpful in case of a problematic function in need of more thorough debugging.

11. _(Optional)_ Using Eclipse debugging, you can examine the host and kernel code in more detail. All the controls for step-by-step debugging are in the Run menu or on the main toolbar menu.

12. Resume to the next breakpoint:
    1. Press **F8**.
    2. Select **Run**>**Resume**.

       ![error: missing image](./images/debug_configuration_hw.png)

13. After resuming debugging, the SDx tool launches another gdb instance for the kernel code, which also has a breakpoint at the beginning of the function.

    You can use this breakpoint to perform detailed analysis of the kernel and how the data looks being read into the function and written out to memory. After the kernel execution is done in gdb, that instance is terminated, and you return to the main debugging thread.

14. To continue, press **F8**.

    >**NOTE**  The console view still shows the kernel debug outputs. Click ![error: missing image](./images/gqm1517357172417.png) to go back to the vadd.exe console and see the output from the host code.

15. To close the Debug perspective, do one of the following:

    * In the upper-right corner of the window showing ![error: missing image](./images/cwo1517357172495.png) (Debug), right-click, and select **Close**.
    * Click ![error: missing image](./images/sdx_perspective_icon.PNG) (SDx) to switch to the standard SDx perspective.

16. After you are in the main SDx Perspective, close all tabs in the center Project Editor window _except_ the Application Project Settings window.

## Step 3: Run Hardware Emulation

This step covers running the Hardware Emulation feature, as well as looking at the basics of profiling and reports.

The main difference between Emulation-SW and Emulation-HW is that when you emulate hardware, it builds a design that is closer to what is seen on the platform, synthesizing RTL for the kernel code. This means that data related to bandwidth, throughput, and execution time are more accurate. However, this causes the design to take longer to compile.

1. To run Hardware Emulation, go to SDx Application Settings, and ensure that **Active build configuration** is set to Emulation-HW, and then click **Run**. This takes some time to complete.

2. In the Assistant tab, under the Emulation-HW configuration, open the System Estimate report. This text report provides information related to kernel information, timing about the design, clock cycles, and area used in the device.

   ![error: missing image](./images/183_system_estimate_hw.png)

3. In the Reports tab, double click to open the Profile Summary report. This report provides detailed information related to kernel operation, data transfers, and OpenCL API calls, as well as profiling information related to the resource usage, and data transfer to/from the kernel/host.

   >**NOTE**: The simulation models used in Hardware Emulation are approximate. The profile numbers shown are just an estimate and might vary from results obtained in real hardware.

   ![error: missing image](./images/183_profile_summary_report_hw.png)

   Next to the Console tab, the Guidance tab is displayed. This is where unmet checks provide some information on how to optimize the kernel.

   ![error: missing image](./images/183_guidance_view_hw.png)

   >**NOTE**: To see other performance optimization techniques and methodologies, refer to the _SDAccel Profiling and Optimization Guide_ ([UG1207](https://www.xilinx.com/cgi-bin/docs/rdoc?v=2018.3;d=ug1207-sdaccel-optimization-guide.pdf)).

4. In the Reports tab, double-click to open the Application Timeline report. This report shows the estimated time it takes for the host and kernel to complete the task and provides finer grained information on where bottlenecks can be. Adding a marker, zooming, and expanding signals can help in identifying bottlenecks.

   ![error: missing image](./images/183_timeline_hw.png)  

5. To open the HLS Report, expand the Emulation-HW tab and then expand the relevant kernel tab.

   This report provides detailed information provided by Vivado® HLS on the kernel transformation and synthesis. The tabs at the bottom of the report provide more information on where most of the time is spent in the kernel and other performance related data. Performance data including latency and clock period are also shown in this report.

   ![error: missing image](./images/183_hls_hw.png)  

## Step 4: Use the Makefile Flow

This step explains the basics of the makefile flow and how the SDx™ IDE uses it. The advantages of using this flow include:

* Easy automation into any system
* Faster turnaround time on small design changes

1. In the Project Explorer, navigate to the Emulation-SW directory, and then look for the makefile file.
2. Double-click the file to open it in the editor. The SDx IDE creates this makefile and uses it for building and running emulations. Alternatively, you can navigate to the `Emulation-HW` directory and look for the makefile file.

   Notice that there is a unique makefile for each build. In the opened makefile in the editor window, look at line 21. Note that it specifies a target if either `hw_emu` or `sw_emu`.

   >**TIP**: You can also use the makefile produced by the SDx IDE to build the project outside of the GUI.

3. Open a new terminal session and navigate to the workspace.

4. Navigate to the Emulation-SW directory and enter `make incremental`.

   The process produces a typical SDx log output.

   >**NOTE**: If no changes are made to the host or kernel code, this will do nothing because the compilation is already completed. It might display a message such as:  
   >
   >_make: Nothing to be done for `incremental`._

[Lab 2: Introduction to the SDAccel Makefile](./lab-2-introduction-to-the-sdaccel-makefile.md) goes into more detail on how to use the makefile and command line flow.

## Summary

After completing this tutorial, you know how to do the following:

* Create an SDAccel environment project from a GitHub example design.
* Create a binary container and accelerator for the design.
* Run Software Emulation and use the Debug environment on host and kernel code.
* Run Hardware Emulation and use the reports to understand possible optimization.
* Understand the differences between Software and Hardware Emulation reports.
* Read the project makefile and run the makefile command line.

## Lab 2: Introduction to the SDAccel Makefile

[Lab 2: Introduction to the SDAccel Makefile](./lab-2-introduction-to-the-sdaccel-makefile.md) goes into more detail on how to use the makefile and command line flow.

<hr/>
<p align="center"><sup>Copyright&copy; 2019 Xilinx</sup></p>
