<table>
 <tr>
   <td align="center"><img src="https://www.xilinx.com/content/dam/xilinx/imgs/press/media-kits/corporate/xilinx-logo.png" width="30%"/><h1>2019.1 SDAccel™ Development Environment Tutorials</h1>
   <a href="https://github.com/Xilinx/SDAccel-Tutorials/branches/all">See other versions</a>
   </td>
 </tr>
 <tr>
 <td align="center"><h1>Applying Methodology for Creating an Optimized Accelerated FPGA Application
 </td>
 </tr>
</table>

# C. Methodology for Architecting an FPGA Accelerated Application

## C.1 Baseline the Application Performance and Establish Goals

As stated in the *SDAccel Methodology Guide* ([UG1346](https://www.xilinx.com/support/documentation/sw_manuals/xilinx2019_1/ug1346-sdaccel-methodology-guide.pdf)), you first use the gprof tool to profile the application, and identify potential function for acceleration.

1. Add the `-pg` option in gcc (this is already done in the `Makefile`).
2. cd into the `cpu_src` folder, and run `make` to generate the executable file.

   ```
   cd cpu_src
   ```

3. Run the executable file.

   ```
   ./convolve --gray true ../video.mp4
   ```

4. Extract the profile result.

   ```
   gprof convolve gmon.out> gprofresult.txt
   ```

5. To view the profiling report, open the `gprofresult.txt` file. You should see results similar to the following table.

   Each sample counts as 0.01 seconds.

   | % Time | Cumulative Seconds | Self Seconds | Total Calls  | ms/Call  | ms/Call  | Name                         |  
   |--------:|-----------:|----------:|----------:|----------:|----------:|:------------------------------|  
   | 95.29  |     7.28  |   7.28   |    132   |  55.15   |  55.15   | convolve_cpu                 |
   |  4.85  |     7.65  |   0.37   |    132   |   2.81   |   2.81   | grayscale_cpu                |
   |  0.00  |     7.65  |   0.00   |    132   |   0.00   |   0.00   | print_progress(int, int)     |
   |  0.00  |     7.65  |   0.00   |      1   |   0.00   |   0.00   | GLOBAL__sub_I_default_output |

   The report indicates that the convolve_cpu uses 95% of the execution time. Accelerating that function will significantly improve the total performance.

## C.2 Determine the Maximum Achievable Throughput

In most FPGA-accelerated systems, the maximum achievable throughput is limited by the PCIe bus. The PCIe performance is influenced by many different aspects, such as motherboard, drivers, targeted shell, and transfer sizes. The SDAccel environment provides a utility `xbutil`, and you can run the `xbutil dmatest` command to measure the maximum PCIe bandwidth it can achieve. Your design's target throughput cannot exceed this upper limit.

## C.3 Establish Overall Acceleration Goals

This video application needs to process 30 frames per second at the minimum. Based on this goal, here is the throughput requirements.
The input video frame is in 1920x1080 format, and each RGBA sample is in 4 bytes. Therefore, one frame of data contains 1920x1080x4=8.29MB.
The Total Bandwidth is calculated as:

```
(# of frames * Image Size * size of RGBPixel)/elapsed time/1000000 MBps
```

This is equivalent to:

```
(132*1920*1080*4 /7.28)/1000000 = 150MBps
```

Running purely CPU-based run, Video Processed is 1094 MB in 7.28s which is close to 150MBps.

To achieve 30fps target, your processing throughput should be > 8.29MB*30fps=249MBps.

You must accelerate the application with 250MBps for a live video application, and this tutorial will walk thought achieving that goal.

## Next Step

Now you have identified the functions for acceleration and establish the performance goals.

In the next step, you will create baseline of the original function in hardware and perform a series of host and kernel code optimizations to meet your goals.

You will be using Hardware Emulation runs for measuring performance in each step. As part of the final step, you can run all of these steps in hardware to demonstrate how each step improved the performance.

-------------------------------------------------
<p align="Center"><b>Start the Next Lab: <a href="baseline.md">1. Creating an SDAccel Application from a C Application</a></b></p><p align="Center"><b>Back to Previous Lab:  <a href="HowToRunTutorial.md">B. Understanding the Makefile </a></b></p>

</br>
<hr/>
<p align="center"><a href="/docs/sdaccel-getting-started/">Return to Getting Started Pathway</a> — <a href="./README.md">Return to Start of Tutorial</a></p>
</br>
<p align="center"><sup>Copyright&copy; 2019 Xilinx</sup></p>