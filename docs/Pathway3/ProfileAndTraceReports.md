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

# 3. Generating Profile and Trace Reports

## Introduction

To better understand the design, you can generate a Profile Summary and Application Timeline reports.  

* The Profile Summary provides annotated details regarding the overall application performance.  
* The Application Timeline collects and displays host and device events on a common timeline to help you understand and visualize the overall health and performance of your systems.

The Profile Summary and Timeline Trace reports are available for all build targets (`sw_emu`, `hw_emu`, and `hw`). However, the granularity of the reports can be different for different build targets. For example, with the `sw_emu` build, the Profile Summary report will not include any data transfer details under kernel execution efficiency and data transfer efficiency.

This lab uses hardware emulation to illustrate the steps of profiling. The steps here can be easily migrated to other flows by targeting `sw_emu` or `hw` (`-t sw_emu` or `-t hw`).

>**IMPORTANT:** Turning on device profiling for the system build (`-t hw`) is intrusive and can negatively affect overall application performance. This feature should only be used for performance debugging, and should be disabled in the production build.

## Before You Begin

Before running this lab, it is required to run the [Building an Application](./BuildingAnApplication.md) lab and [Running Software and Hardware Emulation](./Emulation.md) lab.

## Generating Emulation Data

The Profile Summary and Application Timeline reports are not enabled by default because the generated emulation data requires more time to run and added disk space. Therefore, you must enable the profiling data collection before running emulation. This is done by setting options in an `sdaccel.ini` text file, located in the same directory as the host program, in this case the `./reference-files/run` directory. For more information on the `sdaccel.ini` file, see the *SDAccel Environment User Guide* ([UG1023](https://www.xilinx.com/html_docs/xilinx2019_1/sdaccel_doc/itd1534452174535.html)).

### Create the sdaccel.ini file

First, create the `sdaccel.ini` file, and add the required options.

1. In the execution directory, create a file called `sdaccel.ini`.

2. Enter following lines into this file.

   ```
   [Debug]
   profile=true
   timeline_trace=true
   data_transfer = <coarse|fine|off>
   ```
   
   >**Command Options and Descriptions**
   >
   >* `[Debug]`: Debug specific commands to follow
   >* `profile=true`: Enable profile monitoring
   >* `timeline_trace=true`: Enable timeline trace information gathering
   >* `data_transfer_trace=fine`: Enables device-level AXI data transfer information.
   >   * `fine`: Show all AXI-level burst data transfers.
    >   * `coarse`: Show the compute unit (CU) transfer activity from the beginning of first transfer to end of last transfer (before compute unit transfer ends).
      >   * `off`: Turn off reading and reporting of device-level trace during runtime.

3. Save and close the file.

### Build and Run Hardware Emulation

With profiling and the timeline trace enabled in the `sdaccel.ini` file, performance data is gathered during emulation and saved to CSV files. However, it is also necessary to build the kernel using the `--profile_kernel` option during the hardware linking stage to allow performance information to be captured. For more information on `--profile_kernel`, refer to the *SDAccel Environment Profiling and Optimization Guide* ([UG1207](https://www.xilinx.com/html_docs/xilinx2019_1/sdaccel_doc/zgr1534452172723.html)).

1. Rebuild the kernel using the following commands.

     ```bash
     xocc -t hw_emu --platform  xilinx_u200_xdma_201830_1 -g -c -k mmult -I'../src' -o'mmult.hw_emu.xilinx_u200_xdma_201830_1.xo' '../src/mmult.cpp'
     xocc -t hw_emu --platform xilinx_u200_xdma_201830_1 -g -l --nk mmult:1:mmult_1 --profile_kernel data:all:all:all -o'mmult.hw_emu.xilinx_u200_xdma_201830_1.xclbin' mmult.hw_emu.xilinx_u200_xdma_201830_1.xo
     ```

   This generates a new xclbin file which supports profiling. For details on building a design, refer to the [Building an Application](./BuildingAnApplication.md) lab.
2. After the build is complete, run emulation using the following commands.  

   ```bash
   emconfigutil --platform xilinx_u200_xdma_201830_1
   export XCL_EMULATION_MODE=hw_emu
   ./host mmult.hw_emu.xilinx_u200_xdma_201830_1.xclbin
   ```

 >**TIP:** For details on running emulation, refer to the [Running Software and Hardware Emulation](./Emulation.md) lab.

### Viewing Debug Information

After running emulation, the following two CSV files are generated in the execution directory.

* `profile_summary.csv` (Profile Report)
* `timeline_trace.csv` (Timeline Trace)

### View the Profile Summary

To view the Profile Summary, you can open the CSV file in a spreadsheet tool, or use the `sdx_analyze` utility to convert the data into an HTML report format. For more information on the `sdx_analyze` utility, see to the *SDAccel Environment Profiling and Optimization Guide* ([UG1207](https://www.xilinx.com/html_docs/xilinx2019_1/sdaccel_doc/zgr1534452172723.html)).

Run the following command:

```bash
sdx_analyze profile -f html -i ./profile_summary.csv
```

You can open the HTML file in a browser to view the results: `firefox profile_summary.html`.

In the Profile Summary report, as shown in the following figure, you can see information related to kernel execution time, host to global memory data transfer and kernel to global memory data transfer.

  ![ProfileReport](./images/profile_report.png)

### View the Timeline Trace

To view the Timeline Trace, create a waveform file from the CSV file.

1. Run the following command.

   ```bash
   sdx_analyze trace -f wdb -i ./timeline_trace.csv
   ```

2. Using the SDx environment, you can open the generated timeline trace waveform database with the following command.

   ```bash
   sdx -workspace workspace -report timeline_trace.wdb
   ```

   The Timeline Trace report will be similar to the following figure.
   ![Timeline](./images/timeline_trace.png)

   In Timeline Trace view, on a timeline base, you will see information about the events including kernel running, data read and write from host, OpenCL API calls, etc.

   For more information about interpreting the Profile Summary and Timeline Trace reports, refer to the *SDAccel Environment Profiling and Optimization Guide* ([UG1207](https://www.xilinx.com/html_docs/xilinx2019_1/sdaccel_doc/zgr1534452172723.html)).

## Putting it All Together

Below is a summary of all the steps necessary to generate and view Profile Summary and Timeline Trace reports.  

1. Create `sdaccel.ini` file.

   ```bash
   [Debug]
   profile=true
   timeline_trace=true
   data_transfer = fine
   ```

2. Build the platform with `--profile_kernel` option.

   ```bash
   xocc -t hw_emu --platform xilinx_u200_xdma_201830_1 -g -l --nk mmult:1:mmult_1 --profile_kernel data:all:all:all -o'mmult.hw_emu.xilinx_u200_xdma_201830_1.xclbin' mmult.hw_emu.xilinx_u200_xdma_201830_1.xo
   ```

3. Run the application.

   ```bash
   emconfigutil --platform xilinx_u200_xdma_201830_1
   export XCL_EMULATION_MODE=hw_emu
   ./host mmult.hw_emu.xilinx_u200_xdma_201830_1.xclbin
   ```

4. Convert the Profile Summary and Timeline Trace reports to viewable formats.

   ```bash
   sdx_analyze profile -f html -i ./profile_summary.csv
   sdx_analyze trace -f wdb -i ./timeline_trace.csv
   ```

5. View the reports.  

   ```bash
   firefox profile_summary.html
   sdx -workspace workspace -report timeline_trace.wdb
   ```

# Next Steps

After you perform emulation to check correctness and performance, run application in a real device. The [Execute in Hardware](./HardwareExec.md) lab details how to run application in hardware.

</br>
<hr/>
<p align="center"><b><a href="/docs/sdaccel-getting-started/">Return to Getting Started Pathway</a> — <a href="./README.md">Return to Start of Tutorial</a></b></p>

<p align="center"><sup>Copyright&copy; 2019 Xilinx</sup></p>