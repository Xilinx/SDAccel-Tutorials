<table style="width:100%">
  <tr>
    <th width="100%" colspan="6"><img src="https://www.xilinx.com/content/dam/xilinx/imgs/press/media-kits/corporate/xilinx-logo.png" width="30%"/><h1>SDAccel Development Environment Getting Started Tutorial</h2>
</th>
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
     ``cd <workspace>/examples/getting_started/misc/vadd``

     In this directory, run the `ls` command and view the files. You should see the following contents:
     ````
     [sdaccel@localhost vadd ]$ ls
     Makefile    README.md    description.json src
     ````
     If you run the `ls` on the `src` directory, you should see the following:
     ````
     [sdaccel@localhost vadd ]$ ls src  
     host.cpp    krnl_vadd.cl    vadd.h  
     ````
</details>

<details>
<summary><strong>Step 2: Initial Design and Makefile Exploration</strong></summary>  

  1. The vadd directory contains the Makefile file, which you will use to compile the design in both Hardware and Software Emulation, as well as to generate a System Run.

  2. Open the Makefile in a text editor. View the content and become familiar with how it is written. Makefiles are written in a bash style syntax.  
     >**:pushpin: NOTE:** The file itself makes references to generic makefiles that are used by all the Github example designs.  

  3. The first few lines contain `include` statements for other generic makefiles that are used by all the examples.  
     ````
     COMMON_REPO:=../../../  
     include $(COMMON_REPO)/utility/boards.mk  
     include $(COMMON_REPO)/libs/xcl2/xcl2.mk  
     include $(COMMON_REPO)/libs/opencl/opencl.mk  
     ````
  4. Open the `../../../utility/boards.mk` file. This makefile contains the flags and command line compiler info needed to build the host and source code.   
     ````
     # By Default report is set to none, no report will be generated  
     # 'estimate' for estimate report generation  
     # 'system' for system report generation  
     REPORT:=none  

     # Default C++ Compiler Flags and xocc compiler flags  
     CXXFLAGS:=-Wall -O0 -g  
     CLFLAGS:= --xp "param:compiler.preserveHlsOutput=1" --xp "param:compiler.generateExtraRunData=true" -s  

     ifneq ($(REPORT),none)  
     CLFLAGS += --report $(REPORT)  
     endif
     ````
     `REPORT` is an input flag (parameter) for the `make` command in the terminal. Notice that the `CLFLAGS` is building a long list of `xocc` command line flags to be used.  

  5. Scroll down to line 54 and you will see:  
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

  6. Close the boards.mk file and refocus on the Makefile. Looking at line 9 and beyond, notice that this file handles the majority of where the source code is located, and names the kernel and application executables.  

  7. Finally, open the `../../../utility/rules.mk file`. This file is where all the setup items from the previous makefiles are handled into creating the xocc and the xcpp (gcc) command line arguments. Explore this file until you feel comfortable with what it does. Key areas to focus are labeled with `define make_exe` (line 34) and `define make_xclbin` (line 107).

</details>

<details>
<summary><strong>Step 3: Running Software Emulation</strong></summary>

Now that you understand parts of the makefile construction, it is time to compile the code to run Software Emulation.  

  1. To compile the application for Software Emulation, run the following command:  
     `make all REPORT=estimate TARGETS=sw_emu DEVICES=xilinx_kcu1500_dynamic_5_0`  

     The three files that are generated are:  

     * vadd (host executable)  
     * `xclbin/krnl_vadd.sw_emu.xilinx_kcu1500_dynamic.xclbin` (binary container)  
     * A system estimate report

     To double check that these files were generated, run an `ls` command in the directory and you should get the following:  
     ```
      [sdaccel@localhost vadd]$ ls   
      description.json
      Makefile
      README.md
      src  
      vadd  
      _x  this directory contains the logs and reports from the build process.
      xclbin  
      [sdaccel@localhost vadd ]$ ls xclbin/  
      krnl_vadd.sw_emu.xilinx_kcu1500_dynamic.xclbin  
      krnl_vadd.sw_emu.xilinx_kcu1500_dynamic.xo
     ```

  2. To run the application in emulation, run the following command:  
     `emconfigutil --platform xilinx_kcu1500_dynamic_5_0 --nd 1`
     The `emconfigutil` tool generates a `emconfig.json` file, which contains the information about the target device. However, from the Github repository, the makefile is how you will generate it. Run this command:
     `make check PROFILE=yes TARGETS=sw_emu DEVICES=xilinx_kcu1500_dynamic_5_0`  

     >**:pushpin: NOTE:**  Make sure that the `DEVICES` specified above is the same as what was used for compilation in Step 1.  

     In this flow, this will run the previous command, and also run the application.  

  3. If the application runs successfully, the following messages appear in the terminal:  
      ```
      [sdaccel@localhost vadd]$ make check TARGETS=sw_emu DEVICES=xilinx_kcu1500_dynamic_5_0  
      <install location>/SDx/2017.4/bin/emconfigutil --platform xilinx_kcu1500_dynamic_5_0 --nd 1  

      ****** configutil v2017.4 (64-bit)  
        **** SW Build 2064444 on Sun Nov 19 18:07:27 MST 2017  
          ** Copyright 1986-2017 Xilinx, Inc. All Rights Reserved.  

      INFO: [ConfigUtil 60-895]    Target platform: <install location>/SDx/2017.4/platforms/xilinx_kcu1500_dynamic_5_0/xilinx_kcu1500_dynamic_5_0.xpfm  
      emulation configuration file `emconfig.json` is created in current working directory   

      ...  
      platform Name: Intel(R) OpenCL  
      Vendor Name : Xilinx  
      platform Name: Xilinx  
      Vendor Name : Xilinx  
      Found Platform  
      XCLBIN File Name: krnl_vadd  
      INFO: Importing xclbin/krnl_vadd.sw_emu.xilinx_kcu1500_dynamic.xclbin  
      Loading: 'xclbin/krnl_vadd.sw_emu.xilinx_kcu1500_dynamic.xclbin'  
      Result Match: i = 0 CPU result = 0 Krnl Result = 0  
      Result Match: i = 1 CPU result = 3 Krnl Result = 3  
      Result Match: i = 2 CPU result = 6 Krnl Result = 6  
      Result Match: i = 3 CPU result = 9 Krnl Result = 9  
      Result Match: i = 4 CPU result = 12 Krnl Result = 12  
      Result Match: i = 5 CPU result = 15 Krnl Result = 15  
      ...  
      Result Match: i = 1018 CPU result = 3054 Krnl Result = 3054  
      Result Match: i = 1019 CPU result = 3057 Krnl Result = 3057  
      Result Match: i = 1020 CPU result = 3060 Krnl Result = 3060  
      Result Match: i = 1021 CPU result = 3063 Krnl Result = 3063  
      Result Match: i = 1022 CPU result = 3066 Krnl Result = 3066  
      Result Match: i = 1023 CPU result = 3069 Krnl Result = 3069  
      TEST PASSED  
     ```

  4. If you want to generate additional reports you will need to either set environment variables or create a file called `sdaccel.ini` with appropriate information and permissions.
     In this tutorial, you will create the `sdaccel.ini` file in the `vadd` directory, and add the following contents:  
     ```
      [Debug]  
      timeline_trace = true  
      profile = true  
     ```

  5. Again, run the command:  
     `make check PROFILE=yes TARGETS=sw_emu DEVICES=xilinx_kcu1500_dynamic_5_0`  
     After the application completes, there is an additional timeline trace file called sdaccel_timeline_trace.csv. To view this trace report in the GUI, convert the CSV file into a WDB file using this command:  
     `sdx_analyze trace sdaccel_timeline_trace.csv`  

  6. The application generates a profiling summary report called `sdaccel_profile_summary` in CSV format.  
     You can convert this into a report shown in the Lab 1 profile summary and explore it in the SDx™ IDE. To do this, run the following command:  
     `sdx_analyze profile sdaccel_profile_summary.csv`  
     This generates an `sdaccel_profile_summary.xprf` file. To view this report, open the SDx IDE, select **File > Open File**, and click the file from the menu. The report is shown below.  
     >**:pushpin: NOTE:** For viewing these reports, you do not need to use the workspace you previously used in Lab 1. You can use this command to create a workspace locally for viewing these reports: `sdx -workspace ./lab2`. You may also need to close the Welcome Window to view the report.  

     ![](./images/xci1517374817422.png)  

     >**:pushpin: NOTE:** Software Emulation does not provide all the profiling information (data transfer between kernel and global memory). This information is available in Hardware Emulation and System.  

  7. The System Estimate report (`system_estimate.xtxt`) is also generated. This is from the `--report` switch used when compiling using the `xocc` command.  
     ![](./images/kkq1517374817434.png)  

  8. As you did earlier, launch the SDx IDE.

  9. Select **File > Open File** to locate the `sdaccel_timeline_trace.wdb` file. This opens the report shown in the following figure:  
     ![](./images/rth1517374817491.png)  
</details>

<details>
<summary><strong>Step 4: Running Hardware Emulation</strong></summary>

  1. Now that Software Emulation is complete, you can run Hardware Emulation. To do this without changing the makefile, run the following command:  
     `make all REPORT=estimate TARGETS=hw_emu DEVICES=xilinx_kcu1500_dynamic_5_0`
     When you define the `TARGETS` this way, it passes the value and overwrites the default that was set in the makefile.  
     >**:pushpin: NOTE:** Hardware Emulation takes longer to compile than the Software Emulation.  
     Next, you can re-run the compiled host application. You do not need to regenerate `emconfig.json` because the device information has not changed. However, the emulation needs to be set for Hardware Emulation.  

  2. Re-run the host application with the following command:  
     `make check TARGETS=hw_emu DEVICES=xilinx_kcu1500_dynamic_5_0`  
     >**:pushpin: NOTE:** The makefile sets the enviornment variable to `hw_emu`.  

  3. The output should be similar to the Software Emulation with the following output.  
     ```
      [sdaccel@localhost vadd]$ make check TARGETS=hw_emu DEVICES=xilinx_kcu1500_dynamic_5_0  
      /<install location>/SDx/<version>/bin/emconfigutil --platform xilinx_kcu1500_dynamic_5_0 --nd 1  
      ...  
      INFO: [ConfigUtil 60-895]    Target platform: <install location>/SDx/<version>/platforms/xilinx_kcu1500_dynamic_5_0/xilinx_kcu1500_dynamic_5_0.xpfm  
      emulation configuration file `emconfig.json` is created in current working directory   
      ...  
      platform Name: Intel(R) OpenCL  
      Vendor Name : Xilinx  
      platform Name: Xilinx  
      Vendor Name : Xilinx  
      Found Platform  
      XCLBIN File Name: krnl_vadd  
      INFO: Importing xclbin/krnl_vadd.sw_emu.xilinx_kcu1500_dynamic.xclbin  
      Loading: 'xclbin/krnl_vadd.sw_emu.xilinx_kcu1500_dynamic.xclbin'  
      Result Match: i = 0 CPU result = 0 Krnl Result = 0  
      Result Match: i = 1 CPU result = 3 Krnl Result = 3  
      Result Match: i = 2 CPU result = 6 Krnl Result = 6  
      Result Match: i = 3 CPU result = 9 Krnl Result = 9  
      Result Match: i = 4 CPU result = 12 Krnl Result = 12  
      Result Match: i = 5 CPU result = 15 Krnl Result = 15  
      ...  
      Result Match: i = 1018 CPU result = 3054 Krnl Result = 3054  
      Result Match: i = 1019 CPU result = 3057 Krnl Result = 3057  
      Result Match: i = 1020 CPU result = 3060 Krnl Result = 3060  
      Result Match: i = 1021 CPU result = 3063 Krnl Result = 3063  
      Result Match: i = 1022 CPU result = 3066 Krnl Result = 3066  
      Result Match: i = 1023 CPU result = 3069 Krnl Result = 3069  
      INFO: [SDx-EM 22] [Wall clock time: 10:42, Emulation time: 0.010001 ms]
      TEST PASSED  
     ```

  4. To view the profile summary and timeline trace, run the following commands to convert them for the SDx IDE to read and view the updated information below:  
     ```
      sdx_analyze profile sdaccel_profile_summary.csv  
      sdx_analyze trace sdaccel_timeline_trace.csv  
     ```
     For the Profile Summary, you should see something similar to the following figure.  
     ![](./images/sdx_makefile_hw_emulation_summary.png)    
</details>

<details>
<summary><strong>Step 5: Running System Run</strong></summary>

  1. To compile for a System Run, run the following command:  
     `make check TARGETS=hw DEVICES=xilinx_kcu1500_dynamic_5_0`  
     >**:pushpin: NOTE:** Building for System could take a long time depending on computer resources.  

  2. Once the build is complete, prepare the board installation by using the following command:  
     `xbinst --platform xilinx_kcu1500_dynamic_5_0 -z -d `  
     Where:  
     * `--platform` is the platform to be used by the design.  
     * `-z` archives the board installation files for deployment.  
     * `-d` is the destination directory to use (Required).  

  3. Once complete, a folder called `xbinst` is created that contains all the files and scripts needed to deploy the design. To do this, run the `install.sh` script. The script installs the appropriate libraries, and firmware, and creates a setup.sh to be used to setup the runtime environment.  

  4. Run setup.sh to prepare the runtime environment.  
     >**:pushpin: NOTE:** Running setup.sh requires elevated permissions.  

  5. With the System Run completed, you can re-run this in emulation if desired. Re-run the following command:  
     `make check TARGETS=hw_emu DEVICES=xilinx_kcu1500_dynamic_5_0`  
     >**:pushpin: NOTE:** Running this command with the `TARGET` set to `hw` results in a runtime error on locating a platform.  
     As in the earlier step, the following reports are generated: profile summary, timeline trace, and system estimates.  
Notes from Joyce: I am not quite sure the purpose of this step. Why hardware emulation is executed here in the hardware run step? 


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
