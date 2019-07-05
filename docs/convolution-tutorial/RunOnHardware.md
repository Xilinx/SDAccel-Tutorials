
<table>
 <tr>
   <td align="center"><img src="https://www.xilinx.com/content/dam/xilinx/imgs/press/media-kits/corporate/xilinx-logo.png" width="30%"/><h1>2019.1 SDAccel™ Development Environment Tutorials</h1>
   <a href="https://github.com/Xilinx/SDAccel-Tutorials/branches/all">See other versions</a>
   </td>
 </tr>
 <tr>
 <td align="center"><h1>Methodology for Optimizing Accelerated FPGA Applications
 </td>
 </tr>
</table>

# 7. Running the Accelerator in Hardware

Until now, the results of all the previous labs have been run in hardware emulation mode to give you an idea of how the optimization improves performance, while reducing the compilation time needed to build the system. In this section, you will build and run each of the previous optimizations in hardware on an Alveo accelerator card.

After each run is finished, record the performance data from the Timeline Trace report, and fill in the table at the end of the section. Your numbers might vary.  
Note the following data:

* **Total Data**: Calculated by (frame number x frame size).
* **Total Time**: Measured in hardware Timeline Trace report. For a fair comparison, this will include the data transfer and kernel execution time.
* **Throughput**: Calculated by Total Data Processed(MB)/Total Time(s)

>**IMPORTANT**: Each of the steps in this lab compiles the hardware kernel and can take significant time to complete.

## Run the Baseline Application on Hardware

Use the following commands to run on hardware, generate, and view the Timeline Trace report.

```
make run TARGET=hw STEP=baseline SOLUTION=1
make gen_report TARGET=hw STEP=baseline
make view_timeline_trace TARGET=hw STEP=baseline
```

You should see a Timeline Trace report similar to the following figure.

![][baseline_hw_timeline]

The two markers record the start and end point of the execution, so the execution time can be roughly calculated as 761.52-1.86 = 759.66s.

The total MBs processed is 1920 x 1080 x 4(Bytes) x 132(frames) = 1095 MBs. Therefore, the throughput can be calculated as 1095(MB)/759(s)= 1.44 MB/s. Use this number as the benchmark baseline to measure future optimizations.  

## Run the Memory Transfer Lab on Hardware

Use the following commands to run on hardware, generate, and view the Timeline Trace report.

```
make run TARGET=hw STEP=localbuf SOLUTION=1
make gen_report TARGET=hw STEP=localbuf
make view_timeline_trace TARGET=hw STEP=localbuf
```

You should see a Timeline Trace report for the hardware run similar to the following figure.

![][localbuf_hw_timeline]

The two markers record the start and end point of the execution, so execution time can be roughly calculated as 86.56-1.57 = 84.99s.

## Run Fixed Point Lab on Hardware

Use the following commands to run on hardware, generate, and view the Timeline Trace.

```
make run TARGET=hw STEP=fixedpoint SOLUTION=1
make gen_report TARGET=hw STEP=fixedpoint
make view_timeline_trace TARGET=hw STEP=fixedpoint
```

You should see a Timeline Trace report for the hardware run similar to the following figure.

![][fixedtype_hw_timeline]

 The two markers record the start and end point of the execution, so the execution time can be roughly calculated as 27.12-1.6 = 25.52s.

## Run Dataflow Lab on Hardware

Use the following commands to run on hardware, generate, and view the Timeline Trace.

```
make run TARGET=hw STEP=dataflow SOLUTION=1
make gen_report TARGET=hw STEP=dataflow
make view_timeline_trace TARGET=hw STEP=dataflow
```

You should see a Timeline Trace report for the hardware run, similar to the following figure.

![][dataflow_hw_timeline]

The two markers record the start and end point of the execution, so the execution time can be roughly calculated as 4.66-0.26 = 4.4s.

### Run Multiple Compute Units Lab on Hardware

Use the following commands to run on hardware, generate, and view the Timeline Trace.

```
make run TARGET=hw STEP=multicu SOLUTION=1
make gen_report TARGET=hw STEP=multicu
make view_timeline_trace TARGET=hw STEP=multicu
```

You should see a Timeline Trace report for the hardware run, similar to the following figure.

![][hostopt_hw_timeline]

 The two markers record the start and end point of the execution, so execution time can be roughly calculated as 4.077-1.682 = 2.395s.

### Performance Table

The final performance benchmarking table displays as follows.

| Step                            | Image Size   | Number of Frames  | Time (Hardware) (s) | Throughput (MBps) |
| :-----------------------        | :----------- | ------------: | ------------------: | ----------------: |
| baseline                        |     1920x1080 |           132 |              759.66 | 1.44              |
| localbuf                        |     1920x1080 |           132 |                84.99 | 12.9 (8.96x)         |
| fixed-point data                |     1920x1080 |           132 |                25.52 | 43 (3.33x)        |
| dataflow                        |     1920x1080 |           132 |                4.4 | 249 (5.8x)        |
| multi-CU                        |     1920x1080 |           132 |                2.395 | 457 (1.83x)       |

---------------------------------------

[baseline_hw_timeline]:./images/191_baseline_hw_timeline_new.JPG "Baseline version hardware Timeline Trace Report"
[localbuf_hw_timeline]:./images/191_localbuf_hw_timeline_new.JPG "Local buffer version hardware Timeline Trace Report"
[fixedtype_hw_timeline]:./images/191_fixedtype_hw_timeline_new.JPG "Fixed-type data version hardware Timeline Trace Report"
[dataflow_hw_timeline]:./images/191_dataflow_hw_timeline_new.JPG "Dataflow version hardware Timeline Trace Report"
[hostopt_hw_timeline]: ./images/191_hostopt_hw_timeline_new.JPG "Host code optimization version hardware timeline trace report"

## Conclusion

Congratulations! You have successfully completed all the modules of this lab to convert a standard CPU-based application into an FPGA accelerated application, running with nearly 300X the throughput when running on the Alveo U200 accelerator card. You set performance objectives, and then you employed a series of optimizations to achieve your objectives.

1. You created an SDAccel application from a basic C application.
1. You familiarized yourself with the reports generated during software and hardware emulation.
1. You explored various methods of optimizing your HLS kernel.
1. You learned how to set an OpenCL API command queue to execute out-of-order for improved performance.
1. You enabled your kernel to run on multiple CUs.
1. You used the HLS dataflow directive, and explored how it affected your application.
1. You ran the optimized application on the Alveo accelerator card to see the actual performance gains.

</br>
<hr/>
<p align="center"><b><a href="/docs/sdaccel-getting-started/">Return to Getting Started Pathway</a> — <a href="./README.md">Return to Start of Tutorial</a></b></p>

<p align="center"><sup>Copyright&copy; 2019 Xilinx</sup></p>