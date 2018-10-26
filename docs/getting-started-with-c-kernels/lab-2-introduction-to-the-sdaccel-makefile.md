<table style="width:100%">
  <tr>
    <th width="100%" colspan="6"><img src="https://www.xilinx.com/content/dam/xilinx/imgs/press/media-kits/corporate/xilinx-logo.png" width="30%"/><h1>SDAccel Development Environment Getting Started Tutorial</h2>
</th>
  </tr>
    <tr>
    <td><a href="../README.md">:house: HOME </a></td>
    <td colspan="2" align="center"><b>Getting Started with C/C++ Kernels</b></td>
  </tr> 
  <tr>
    <td align="center"><a href="README.md">Introduction</td>
    <td align="center"><a href="lab-1-introduction-to-the-sadccel-developmentenvironment.md">Lab 1: Introduction to the SDAccel Development Environment</td>
    <td align="center">Lab 2: Introduction to the SDAccel Makefile</a></td>
  </tr>
</table>

## Lab 2: Introduction to the SDAccel Makefile  

The following lab uses an example from the Xilinx® SDAccel™ Example Github repository, which can be found [here](https://github.com/Xilinx/SDAccel_Examples).

<details>
<summary><strong>Step 1: Preparing and Setting up the SDAccel Environment</strong></summary>

In this step, you will set up SDx™ to run in command line, and clone the Github repository for SDAccel™.  

  1. Launch a terminal and source the settings scripts found in the SDx environment using the command:
     ``source <SDx_install_location>/<version>/settings64.csh
     ``
     or
     ``source <SDx_install_location>/<version>/settings64.sh
     ``
     This allows you to run the SDx command lines without the need to use the GUI.  

  2. If you downloaded the SDAccel examples through the SDx IDE, as described in lab 1, then you can access the files from that location. On Linux the files are downloaded to `/home/<user>/.Xilinx/SDx/<version>/sdaccel_examples/` to a workspace of your choice using the command:
     ``git clone https://github.com/Xilinx/SDAccel_Examples <workspace>/examples
     ``
     >**:pushpin: NOTE:**  This Github repository totals around 400MB in size. Make sure you have sufficient space on a local or remote disk to ensure that it can be completely downloaded.  

  3. Once the download is complete, navigate to the `vadd` directory in the SDAccel example using the following command:  
     ``cd <workspace>/examples/getting_started/host/helloworld_c``

     In this directory, run the `ls` command and view the files. You should see the following contents:
     ````
     [sdaccel@localhost helloworld_c]$ ls
     Makefile    README.md    description.json src utils.mk
     ````
     If you run the `ls` on the `src` directory, you should see the following:
     ````
     [sdaccel@localhost helloworld_c]$ ls src  
     host.cpp    vadd.cpp  
     ````
</details>

<details>
<summary><strong>Step 2: Initial Design and Makefile Exploration</strong></summary>  

  1. The helloworld_c directory contains the Makefile file, which you will use to compile the design in both Hardware and Software Emulation, as well as to generate a System Run.

  2. Open the Makefile in a text editor. View the content and become familiar with how it is written. Makefiles are written in a bash style syntax.  
     >**:pushpin: NOTE:** The file itself makes references to generic makefiles that are used by all the Github example designs.  

  3. The first few lines contain `include` statements for other generic makefiles that are used by all the examples.  
     ````
     COMMON_REPO = ../../../
     ABS_COMMON_REPO = $(shell readlink -f $(COMMON_REPO))

     include ./utils.mk

     ````
  4. Open the `../../../utility/boards.mk` file. This makefile contains the flags and command line compiler info needed to build the host and source code.   
     ````
     # By Default report is set to none, no report will be generated  
     # 'estimate' for estimate report generation  
     # 'system' for system report generation  
     REPORT:=none
     PROFILE ?= no
     DEBUG ?=no

     # Default C++ Compiler Flags and xocc compiler flags  
     CXXFLAGS:=-Wall -O0 -g -std=c++14
     CLFLAGS:= --xp "param:compiler.preserveHlsOutput=1" --xp "param:compiler.generateExtraRunData=true" -s  

     ifneq ($(REPORT),none)  
     CLFLAGS += --report $(REPORT)  
     endif

     ifeq ($(PROFILE),yes)
     CLFLAGS += --profile_kernel data:all:all:all
     endif

     ifeq ($(DEBUG),yes)
     CLFLAGS += --dk protocol:all:all:all
     endif

     ````
     `REPORT`, `PROFILE` and `DEBUG` are input flags (parameter) for the `make` command in the terminal. Notice that the `CLFLAGS` is building a long list of `xocc` command line flags to be used.  

  5. Scroll down to line 52, and you will see:  
     ````
        # By default build for hardware can be set to  
        #   hw_emu for hardware emulation  
        #   sw_emu for software emulation  
        #   or a collection of all or none of these  
        TARGETS:=hw  

        # By default only have one device in the system  
        NUM_DEVICES:=1  
     ````
     Here, `TARGETS` defines what default build to have (if not specified in the makefile command line). By default, it is set to `hw` (System build). You will be setting this value as desired when working on your own design. Lastly, you can define the number of devices the machine uses that contain the board you selected. Generally, one device is fine to start, but you can change this if your design requires more.  

  6. Close the boards.mk file, and refocus on the Makefile. Looking at line 9 and beyond, notice that this file handles the majority of where the source code is located, and names the kernel and application executables.  

  7. Finally, open the `../../../utility/rules.mk file`. This file is where all the setup items from the previous makefiles are handled into creating the xocc and the xcpp (gcc) command line arguments. Explore this file until you feel comfortable with what it does. Key areas to focus are labeled with `define make_exe` (line 34) and `define make_xclbin` (line 107).

</details>

<details>
<summary><strong>Step 3: Running Software Emulation</strong></summary>

Now that you understand parts of the makefile construction, it is time to compile the code to run Software Emulation.  

  1. To compile the application for Software Emulation, run the following command:  
     `make all REPORT=estimate TARGETS=sw_emu DEVICES=xilinx_u200_xdma_201820_2`  
     When you define the `TARGETS` this way, it passes the value and overwrites the default that was set in the makefile.
     
     The four files that are generated are:  

     * host (host executable)  
     * `xclbin/vadd.sw_emu.xilinx_u200_xdma_201820_2.xclbin` (binary container)  
     * A system estimate report
     * `emconfig.json`

     To double check that these files were generated, run an `ls` command in the directory and you should get the following:  
     ```
      [sdaccel@localhost helloworld_c]$ ls   
      description.json
      Makefile
      README.md
      src  
      host  
      _x  (this directory contains the logs and reports from the build process.)
      xclbin  
      [sdaccel@localhost helloworld_c]$ ls xclbin/  
      vadd.sw_emu.xilinx_u200_xdma_201820_2.xclbin  
      vadd.sw_emu.xilinx_u200_xdma_201820_2.xo
      xilinx_u200_xdma_201820_2 (this folder contains the emconfig.json file)
     ```

  2. To run the application in emulation, run the following command:  
     `make check PROFILE=yes TARGETS=sw_emu DEVICES=xilinx_u200_xdma_201820_2`  

     >**:pushpin: NOTE:**  Make sure that the `DEVICES` specified above is the same as what was used for compilation in Step 1.  

     In this flow, this will run the previous command, and also run the application.  

  3. If the application runs successfully, the following messages appear in the terminal:  
      ```
      [sdaccel@localhost helloworld_c]$ make check TARGETS=sw_emu DEVICES=xilinx_u200_xdma_201820_2
      cp -rf ./xclbin/xilinx_u200_xdma_201820_2/emconfig.json .
      XCL_EMULATION_MODE=sw_emu ./host
      Found Platform
      Platform Name: Xilinx
      XCLBIN File Name: vadd
      INFO: Importing xclbin/vadd.sw_emu.xilinx_u200_xdma_201820_2.xclbin
      Loading: 'xclbin/vadd.sw_emu.xilinx_u200_xdma_201820_2.xclbin'
      TEST PASSED
      sdx_analyze profile -i sdaccel_profile_summary.csv -f html
      INFO: Tool Version : 2018.2
      INFO: Done writing sdaccel_profile_summary.html

      ```

  4. If you want to generate additional reports, you will need to either set environment variables or create a file called `sdaccel.ini` with appropriate information and permissions.
     In this tutorial, you will create the `sdaccel.ini` file in the `helloworld_c` directory, and add the following contents:  
     ```
      [Debug]  
      timeline_trace = true  
      profile = true  
     ```

  5. Again, run the command:  
     `make check PROFILE=yes TARGETS=sw_emu DEVICES=xilinx_u200_xdma_201820_2`  
     After the application completes, there is an additional timeline trace file called sdaccel_timeline_trace.csv. To view this trace report in the GUI, convert the CSV file into a WDB file using this command:  
     `sdx_analyze trace sdaccel_timeline_trace.csv`  

  6. The application generates a profiling summary report called `sdaccel_profile_summary` in CSV format.  
     You can convert this into a report shown in the Lab 1 profile summary and explore it in the SDx™ IDE. To do this, run the following command:  
     `sdx_analyze profile sdaccel_profile_summary.csv`  
     This generates an `sdaccel_profile_summary.xprf` file. To view this report, open the SDx IDE, select **File > Open File**, and click the file from the menu. The report is shown below.  
     >**:pushpin: NOTE:** For viewing these reports, you do not need to use the workspace you previously used in Lab 1. You can use this command to create a workspace locally for viewing these reports: `sdx -workspace ./lab2`. You may also need to close the Welcome Window to view the report.  

     ![](./images/lab2-sw_emu_profile.PNG)  

     >**:pushpin: NOTE:** Software Emulation does not provide all the profiling information (data transfer between kernel and global memory). This information is available in Hardware Emulation and System.  

  7. The System Estimate report (`system_estimate.xtxt`) is also generated. This is from the `--report` switch used when compiling using the `xocc` command.  
     ![](./images/lab2_sw_emu_sysestimate.PNG)  

  8. As you did earlier, launch the SDx IDE.

  9. Select **File > Open File** to locate the `sdaccel_timeline_trace.wdb` file. This opens the report shown in the following figure:  
     ![](./images/lab2-sw_emu_timeline.PNG)  
</details>

<details>
<summary><strong>Step 4: Running Hardware Emulation</strong></summary>

  1. Now that Software Emulation is complete, you can run Hardware Emulation. To do this without changing the makefile, run the following command:  
     `make all REPORT=estimate TARGETS=hw_emu DEVICES=xilinx_u200_xdma_201820_2`
     When you define the `TARGETS` this way, it passes the value and overwrites the default that was set in the makefile.  
     >**:pushpin: NOTE:** Hardware Emulation takes longer to compile than the Software Emulation.  
     Next, you can re-run the compiled host application. You do not need to regenerate `emconfig.json` because the device information has not changed. However, the emulation needs to be set for Hardware Emulation.  

  2. Re-run the host application with the following command:  
     `make check TARGETS=hw_emu DEVICES=xilinx_u200_xdma_201820_2`  
     >**:pushpin: NOTE:** The makefile sets the enviornment variable to `hw_emu`.  

  3. The output should be similar to the Software Emulation with the following output.  
     ```
      [sdaccel@localhost helloworld_c]$ make check TARGETS=hw_emu DEVICES=xilinx_u200_xdma_201820_2
      cp -rf ./xclbin/xilinx_u200_xdma_201820_2/emconfig.json .
      XCL_EMULATION_MODE=hw_emu ./host
      Found Platform
      Platform Name: Xilinx
      XCLBIN File Name: vadd
      INFO: Importing xclbin/vadd.hw_emu.xilinx_u200_xdma_201820_2.xclbin
      Loading: 'xclbin/vadd.hw_emu.xilinx_u200_xdma_201820_2.xclbin'
      INFO: [SDx-EM 01] Hardware emulation runs simulation underneath. Using a large data set will result in long simulation times. It is recommended that a small dataset is used for faster execution. This flow does not use cycle accurate models and hence the performance data generated is approximate.
      TEST PASSED
      INFO: [SDx-EM 22] [Wall clock time: 00:10, Emulation time: 0.109454 ms] Data transfer between kernel(s) and global memory(s)
      vadd_1:m_axi_gmem          RD = 32.000 KB              WR = 16.000 KB       

      sdx_analyze profile -i sdaccel_profile_summary.csv -f html
      INFO: Tool Version : 2018.2
      Running SDx Rule Check Server on port:40213
      INFO: Done writing sdaccel_profile_summary.html
     ```

  4. To view the profile summary and timeline trace, run the following commands to convert them for the SDx IDE to read and view the updated information below:  
     ```
      sdx_analyze profile sdaccel_profile_summary.csv  
      sdx_analyze trace sdaccel_timeline_trace.csv  
     ```
     For the Profile Summary, you should see something similar to the following figure.  
     ![](./images/lab2-hw_emu_profile.PNG)    
</details>

<details>
<summary><strong>Step 5: Running System Run</strong></summary>

  1. To compile for a System Run, run the following command:  
     `make all TARGETS=hw DEVICES=xilinx_u200_xdma_201820_2`  
     >**:pushpin: NOTE:** Building for System could take a long time depending on computer resources.  

  2. Once the build is complete, prepare the board installation by using the following command:  
     `xbinst --platform xilinx_u200_xdma_201820_2 -z -d . `  
     Where:  
     * `--platform` is the platform to be used by the design.  
     * `-z` archives the board installation files for deployment.  
     * `-d` is the destination directory to use (Required).  

  3. Once complete, a folder called `xbinst` is created that contains all the files and scripts needed to deploy the design. To do this, run the `install.sh` script. The script installs the appropriate libraries, and firmware, and creates a setup.sh to be used to setup the runtime environment.  

  4. Run setup.sh to prepare the runtime environment.  
     >**:pushpin: NOTE:** Running setup.sh requires elevated permissions.  

  5. With the System Run completed, you can re-run the following command:  
     `make check TARGETS=hw DEVICES=xilinx_u200_xdma_201820_2`  


  6. Use the following commands to convert the profile summary and timeline trace into files that SDx can read:
     ```
      sdx_analyze profile sdaccel_profile_summary.csv  
      sdx_analyze trace sdaccel_timeline_trace.csv      
     ```
</details>

### Summary

After completing this tutorial, you should be able to do the following:  

  * Set up the SDx™ environment to run all commands in a terminal.  
  * Clone a Github repository.  
  * Run the xcpp, xocc, emconfigutil, sdx_analyze profile, sdx_analyze trace commands to generate the application, binary container, and emulation model.  
  * Write a makefile to compile an OpenCL™ kernel and host code.  
  * View the generated files from emulation in a text editor or the SDx IDE.  
  * Set up the environment and deploy the design to be used with the platform.  
</details>

  <hr/>
  <p align="center"><sup>Copyright&copy; 2018 Xilinx</sup></p>
