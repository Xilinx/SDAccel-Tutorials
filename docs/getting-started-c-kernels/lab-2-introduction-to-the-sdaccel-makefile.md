<table style="width:100%">
  <tr>
    <td align="center" width="100%" colspan="6"><img src="https://www.xilinx.com/content/dam/xilinx/imgs/press/media-kits/corporate/xilinx-logo.png" width="30%"/><h1>2018.3 SDAccel™ Development Environment Tutorials</h1>
    <a href="https://github.com/Xilinx/SDAccel-Tutorials/branches/all">See other versions</a>
</td>
  </tr>
  <tr>
    <td align="center"><a href="README.md">Introduction</td>
    <td align="center"><a href="lab-1-introduction-to-the-sdaccel-gui.md">Lab 1: Introduction to the SDAccel Development Environment</td>
    <td align="center">Lab 2: Introduction to the SDAccel Makefile</a></td>
  </tr>
</table>

# Lab 2: Introduction to the SDAccel Makefile

The following lab uses an example from the Xilinx® SDAccel™ Example GitHub repository, which can be found [here](https://github.com/Xilinx/SDAccel_Examples).

## Step 1: Prepare and Set up the SDAccel Environment

In this step, you will set up the SDx™ environment to run in command line and clone the GitHub repository for SDAccel™.

1. Launch a terminal window and source the settings scripts found in the SDx environment using one of the following commands:

   ```c
   source <SDx_install_location>/<version>/settings64.csh
   ```

   ```c
   source <SDx_install_location>/<version>/settings64.sh
   ```

   This enables you to run the SDx command lines without having to use the GUI.

   If you downloaded the SDAccel examples through the SDx IDE, as described in Lab 1, then you can access the files from that location. On Linux, the files are downloaded to `/home/<user>/.Xilinx/SDx/<version>/sdaccel_examples/` to a workspace of your choice using the following command:

   ```
   git clone https://github.com/Xilinx/SDAccel_Examples <workspace>/examples
   ```

   >**NOTE**: This GitHub repository is about 400 MB in size. Ensure you have enough space on a local or remote disk.

2. After the download is complete, navigate to the `vadd` directory in the SDAccel example using the following command:

   ```C
   cd <workspace>/examples/getting_started/host/helloworld_c
   ```

   In this directory, run the `ls` command and view the files, which displays the following contents:

   ```C
   [sdaccel@localhost helloworld_c]$ ls
   Makefile    README.md    description.json src utils.mk
   ```

   If you run the `ls` command on the `src` directory, it displays the following:

   ```C
   [sdaccel@localhost helloworld_c]$ ls src
   host.cpp    vadd.cpp
   ```

## Step 2: Initial Design and Makefile Exploration

The `helloworld_c` directory contains the Makefile file, which you will use:

* To compile the design in both Hardware and Software Emulation
* To generate a System Run

1. In a text editor, open the Makefile.

2. View the content and become familiar with how it is written, such as the bash style syntax that it uses.

   >**NOTE**: The file itself makes references to generic makefiles that are used by all GitHub example designs.

   The first few lines contain `include` statements for other generic makefiles that are used by all the examples.

   ```c
   COMMON_REPO = ../../../
   ABS_COMMON_REPO = $(shell readlink -f $(COMMON_REPO))

   include ./utils.mk
   ```

3. Scroll down to line 22 and see the following:

   ```c
   TARGETS:=hw
   ```

   Here, `TARGETS` defines what default build to have (if not specified in the makefile command line). By default, it is set to `hw` (System build). When you work on your own design, you will set the desired value.

4. Open the `./utils.mk` makefile, which contains the flags and command line compiler info needed to build the host and source code.

   ```c
   # By Default report is set to none, no report will be generated  
   # 'estimate' for estimate report generation  
   # 'system' for system report generation  
   REPORT:=none
   PROFILE ?= no
   DEBUG ?=no

   ifneq ($(REPORT),none)  
   CLFLAGS += --report $(REPORT)  
   endif

   ifeq ($(PROFILE),yes)
   CLFLAGS += --profile_kernel data:all:all:all
   endif

   ifeq ($(DEBUG),yes)
   CLFLAGS += --dk protocol:all:all:all
   endif
   ```

   >**NOTE**: `REPORT`, `PROFILE` and `DEBUG` are input flags (parameters) for the `make` command in the terminal. Notice that the `CLFLAGS` is building a long list of `xocc` command line flags to be used.

5. Close `utils.mk` and refocus on the Makefile.

6. Review lines 44 and beyond. Note that this file handles the majority of where the source code is located, and names the kernel and application executables.

## Step 3: Run Software Emulation

Now that you understand parts of the makefile construction, it is time to compile the code to run Software Emulation.

1. To compile the application for Software Emulation, run the following command:

   ```C
   make all REPORT=estimate TARGETS=sw_emu DEVICES=xilinx_u200_xdma_201830_1`
   ```

   The four files that are generated are:

   * host (host executable)
   * `xclbin/vadd.sw_emu.xilinx_u200_xdma_201830_1.xclbin` (binary container)
   * A system estimate report
   * `emconfig.json`

2. To verify that these files were generated, run an `ls` command in the directory.

   The following is displayed:

   ```C
   [sdaccel@localhost helloworld_c]$ ls
   description.json
   Makefile
   README.md
   src
   host
   _x  this directory contains the logs and reports from the build process.
   xclbin
   [sdaccel@localhost helloworld_c]$ ls xclbin/
   vadd.sw_emu.xilinx_u200_xdma_201830_1.xclbin
   vadd.sw_emu.xilinx_u200_xdma_201830_1.xo
   xilinx_u200_xdma_201830_1 this folder contains the emconfig.json file
   ```

3. To run the application in emulation, run the following command:

   ```C
   make check PROFILE=yes TARGETS=sw_emu DEVICES=xilinx_u200_xdma_201830_1
   ```

   >**NOTE**: Ensure sure that value for `DEVICES` specified above is the same as what was used for compilation in the **Initial Design and Makefile Exploration** section.

   In this flow, it runs the previous command and the application.

   If the application runs successfully, the following messages appear in the terminal:

   ```C
   [sdaccel@localhost helloworld_c]$ make check TARGETS=sw_emu DEVICES=xilinx_u200_xdma_201830_1
   cp -rf ./xclbin/xilinx_u200_xdma_201830_1/emconfig.json .
   XCL_EMULATION_MODE=sw_emu ./host
   Found Platform
   Platform Name: Xilinx
   XCLBIN File Name: vadd
   INFO: Importing xclbin/vadd.sw_emu.xilinx_u200_xdma_201830_1.xclbin
   Loading: 'xclbin/vadd.sw_emu.xilinx_u200_xdma_201830_1.xclbin'
   TEST PASSED
   sdx_analyze profile -i sdaccel_profile_summary.csv -f html
   INFO: Tool Version : 2018.3
   INFO: Done writing sdaccel_profile_summary.html
   ```

   To generate additional reports, you would do one of the following:

   * set environment variables
   * create a file called `sdaccel.ini` with appropriate information and permissions.

## Step 4: Generate Additional Reports

In this tutorial, you will create the `sdaccel.ini` file in the `helloworld_c` directory, and add the following contents:

```C
[Debug]
timeline_trace = true
profile = true
```

1. Run the command following command:

   ```C
   make check PROFILE=yes TARGETS=sw_emu DEVICES=xilinx_u200_xdma_201830_1
   ```

   After the application completes, there is an additional timeline trace file called `sdaccel_timeline_trace.csv`.

2. To view the trace report in the GUI, convert the CSV file into a WDB file using this command:

   ```C
   sdx_analyze trace sdaccel_timeline_trace.csv
   ```

   The application generates a profiling summary report called `sdaccel_profile_summary` in CSV format.

3. To explore the report in the SDx IDE, convert `sdaccel_timeline_trace.csv` into a report type shown in Lab 1: profile summary. Run the following command:

   ```C
   sdx_analyze profile sdaccel_profile_summary.csv
   ```

   This generates an `sdaccel_profile_summary.xprf` file.

4. To view this report, in the SDx IDE, select **File** > **Open File**, and select the file from the menu.

   The report is shown below.

   ![error: missing image](./images/183_lab2-sw_emu_profile.png)

   >**NOTE**: For viewing these reports, you do not need to use the workspace you previously used in Lab 1. To create a workspace locally for viewing these reports, use the following command:
   >```C
   >sdx -workspace ./lab2
   >```
   >
   > You may also need to close the Welcome Window to view the report.
   >
   >Software Emulation does not provide all the profiling information (such as data transfer between kernel and global memory). This information is available in Hardware Emulation and System.

   The System Estimate report (`system_estimate.xtxt`) is also generated. This is from the `--report` switch used when compiling using the `xocc` command.

   ![error: missing image](./images/183_lab2_sw_emu_sysestimate.png)

5. As you did earlier, in the SDx IDE, select **File** > **Open File** to locate the `sdaccel_timeline_trace.wdb` file, which opens the following report:

   ![error: missing image](./images/183_lab2-sw_emu_timeline.png)

## Run Hardware Emulation

1. After Software Emulation completes, you can run Hardware Emulation. To do this without changing the makefile, run the following command:

   ```C
   make all REPORT=estimate TARGETS=hw_emu DEVICES=xilinx_u200_xdma_201830_1
   ```

   When you define the `TARGETS` this way, it passes the value and overwrites the default that was set in the makefile.

   >**NOTE**: Hardware Emulation takes longer to compile than the Software Emulation.

2. Re-run the compiled host application.

   ```C
   make check TARGETS=hw_emu DEVICES=xilinx_u200_xdma_201830_1
   ```

   >**NOTE**: The makefile sets the environment variable to `hw_emu`.
   >
   >You do not need to regenerate `emconfig.json`, because the device information has not changed. However, the emulation needs to be set for Hardware Emulation.

   The output looks like the Software Emulation output, as shown in the following figure.

   ```C
   [sdaccel@localhost helloworld_c]$ make check TARGETS=hw_emu DEVICES=xilinx_u200_xdma_201830_1
   cp -rf ./xclbin/xilinx_u200_xdma_201830_1/emconfig.json .
   XCL_EMULATION_MODE=hw_emu ./host
   Found Platform
   Platform Name: Xilinx
   XCLBIN File Name: vadd
   INFO: Importing xclbin/vadd.hw_emu.xilinx_u200_xdma_201830_1.xclbin
   Loading: 'xclbin/vadd.hw_emu.xilinx_u200_xdma_201830_1.xclbin'
   INFO: [SDx-EM 01] Hardware emulation runs simulation underneath. Using a large data set will result in long simulation times. It is recommended that a small dataset is used for faster execution. This flow does not use cycle accurate models and hence the performance data generated is approximate.
   TEST PASSED
   INFO: [SDx-EM 22] [Wall clock time: 00:10, Emulation time: 0.109454 ms] Data transfer between kernel(s) and global memory(s)
   vadd_1:m_axi_gmem-DDR[1]          RD = 32.000 KB              WR = 16.000 KB       

   sdx_analyze profile -i sdaccel_profile_summary.csv -f html
   INFO: Tool Version : 2018.3
   Running SDx Rule Check Server on port:40213
   INFO: Done writing sdaccel_profile_summary.html
   ```

3. To view the profile summary and timeline trace, you must convert them for the SDx IDE to read and view the updated information. Use the following command:

   ```C
   sdx_analyze profile sdaccel_profile_summary.csv
   sdx_analyze trace sdaccel_timeline_trace.csv
   ```

   The Profile Summary is displayed, as shown in the following figure.

   ![error: missing image](./images/183_lab2-hw_emu_profile.png)

## System Run

1. To compile for a System Run, use the following command:

   ```C
   make all TARGETS=hw DEVICES=xilinx_u200_xdma_201830_1
   ```

   >**NOTE**: Building for System could take a long time, depending on computer resources.

2. To run the design on U200 acceleration cards, install the board and deployment software, as described in _Getting Started with Alveo Data Center Accelerator Cards_ ([UG1301](https://www.xilinx.com/support/documentation/boards_and_kits/accelerator-cards/ug1301-getting-started-guide-alveo-accelerator-cards.pdf)).

3. After the card is installed successfully and validated, use the following command to run the application:

   ```C
   make check TARGETS=hw DEVICES=xilinx_u200_xdma_201830_1
   ```

4. After system run is finished, to convert the profile summary and timeline trace into files that SDx environment can read, use the following command:

   ```C
   sdx_analyze profile sdaccel_profile_summary.csv
   sdx_analyze trace sdaccel_timeline_trace.csv
   ```

## Summary

After completing this tutorial, you know how to do the following:

* Set up the SDx environment to run all commands in a terminal.
* Clone a GitHub repository.
* Run the xcpp, xocc, emconfigutil, sdx_analyze profile, sdx_analyze trace commands to generate the application, binary container, and emulation model.
* Write a makefile to compile an OpenCL™ kernel and host code.
* View the generated files from emulation in a text editor or the SDx IDE.
* Set up the environment and deploy the design to be used with the platform.

<hr/>
<p align="center"><sup>Copyright&copy; 2019 Xilinx</sup></p>
