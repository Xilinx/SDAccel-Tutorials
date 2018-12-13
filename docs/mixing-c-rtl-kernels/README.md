<table>
 <tr>
   <td align="center"><img src="https://www.xilinx.com/content/dam/xilinx/imgs/press/media-kits/corporate/xilinx-logo.png" width="30%"/><h1>2018.3 SDAccel™ Development Environment Tutorials</h1>
   <a href="https://github.com/Xilinx/SDAccel-Tutorials/branches/all">See other versions</a>
   </td>
 </tr>
 <tr>
 <td align="center"><h3>Mixing C and RTL Kernels</h3>
 </td>
 </tr>
</table>

## Introduction
The SDAccel™ development environment provides a platform to accelerate your functions in hardware. While the host software or application is developed in C/C++ with OpenCL™ API calls, the hardware component or kernel can be developed in C/C++, OpenCL C, or RTL.  In fact, SDAccel environment applications can use any combination of kernels developed in the different languages. This tutorial demonstrates an application using two kernels, where the host code accesses the kernels in an identical manner:
* a kernel developed in C++
* a kernel developed in RTL

### Workflow
In this tutorial, you will use the SDAccel tool in GUI mode to initially create a new project, and add a C++ based kernel. Then, you will generate a binary container (xclbin) containing the C++ based kernel for implementation on an FPGA. Then, run the design in software emulation. You will review the generated Application Timeline report to see the kernel being called and run by the host application.

In the second section, you will create a simple RTL kernel using the RTL Kernel Wizard, and add it to the project. The binary container will be updated to include the RTL based kernel, as well as the C++ kernel. You will update the host code to use the RTL based kernel, and run hardware emulation. Once again, you will review the Application Timeline and see both kernels being called and run by the host application.

## Using C/C++ Kernels
TO begin, launch the SDAccel environment, create a new project, and import sources. For reference, the _SDAccel Environment User Guide_ ([UG1023](https://www.xilinx.com/cgi-bin/docs/rdoc?v=2018.3;d=ug1023-sdaccel-user-guide.pdf)) provides specific details. The [Getting Started with C/C++ Kernels](./docs/getting-started-c-kernels/README.md) lab also walks through the specific steps in more detail.

### Launch the SDAccel environment
1. Open a Linux terminal, and run the following command to launch SDAccel in GUI mode:
```
$ sdx
```  
2. In the Workspace Launcher dialog box, create a new workspace called `Tutorial`, and click **OK** to continue. You can choose any location for the workspace.

### Create a new project with the Project Creation wizard

1. Select **SDx Application Project** from the File / New menu.
2. Specify the following project name: `mixed_c_rtl` and
3. Click **Next**.
4. In the Platform dialog box, select the `xilinx_u200_xdma_201830_1` platform, and then click **Next**.
5. Finally, in the Templates dialog, select **Empty Application**, and then click **Finish**.

### Add Source Files to the prj_c_rtl Project

1. In the SDAccel Project Explorer pane, click on Import Sources, which is circled in red in the following image:  
![Missing Image:ImportSources](images/import_sources_icon.PNG)  
2. Browse to the [`mixing-c-rtl-kernels/reference-files`](/mixing-c-rtl-kernels/reference-files) sub-directory and click OK.
3. Select all the files in the directory, and click **Finish**. This will import the host and C++ kernel source files from the `reference-files` directory to the `mixing_c_rtl` project’s `src` directory.

The kernel (`krnl_vadd.cpp`) adds two input vectors, and generates the output result.
The host code (`host.cpp`) sets up the platform, and defines global memory buffer and connections to the kernel. Four important set of OpenCL API calls in the host code are described below. You can see these calls by opening the `host.cpp` file.

The first set of code, on lines 135-138 of the `host.cpp` file, creates the program to execute. It uses the binary container which contains just the C++ based kernel.

```
cl::Program::Binaries bins;
bins.push_back({buf,nb});
devices.resize(1);
cl::Program program(context, devices, bins);
```

The second, on line 142, gets the C++ `krnl_vadd` kernel object from the program, and assigns the name `krnl_vector_add`. It allows the kernel to be used by the host.

```
cl::Kernel krnl_vector_add(program,"krnl_vadd");
```

The third set of code, on lines 162-165, assigns the `krnl_vector_add` kernel arguments to the buffers:

```
krnl_vector_add.setArg(0,buffer_a);
krnl_vector_add.setArg(1,buffer_b);
krnl_vector_add.setArg(2,buffer_result);
krnl_vector_add.setArg(3,DATA_SIZE);
```


The argument numbers 0, 1, 2, and 3 match the argument order in the `krnl_vadd` definition found in `krnl_vadd.cpp`, as shown below:
```
void krnl_vadd(
                int* a,
                int* b,
                int* c,
                const int n_elements)
```
Finally, on line 171, the following OpenCL API launches the `krnl_vector_add` kernel:

```
q.enqueueTask(krnl_vector_add);
```

When we add the RTL-based code, we will add identical calls for that kernel.
As you can see, the high-level OpenCL calls are independent of the language that the kernel was programmed in.

>**NOTE**: Complete details on host code programming can be found in the _SDAccel Environment Programmers Guide_ ([UG1277](https://www.xilinx.com/support/documentation/sw_manuals/xilinx2018_3/ug1277-sdaccel-programmers-guide.pdf)).

### Create a Binary Container containing the C++ Kernel

1. Under Project Explorer, double-click **project.sdx** to open the SDx Application Project Settings window.  
![Missing Image:OpenSDxAppPrj](images/mixing-c-rtl-kernels_open_sdx_app_prj_settings.PNG)  
By doing this, SDx environment scans all your source files and automatically identifies kernels present in the project. In our case, there is only one. Select it and Click OK
2. Click the lightning bolt icon in the Hardware Function section, as shown below.  
![Missing Image:HW_Functions](images/mixing-c-rtl-kernels_hw_functions.PNG)  
After the you click the lightning bolt icon, the SDx™ development environment scans all your source files, and automatically identifies kernels present in the project (in this case, there is only one kernel).
3. Select the kernel, and then click **OK**.  
A binary container with the default ‘binary_container_1’ name is created containing the krnl_vadd, as shown below.  
![Missing Image:BinContainer](images/mixing-c-rtl-kernels_bin_container_with_cpp.PNG)  

Now, the project is ready to be compiled (i.e., built).

### Compile the Project

1. In the SDx Application Project Settings window, ensure that **Emulation-SW** is the active configuration:  
![Missing Image:SW_EMU](images/mixing-c-rtl-kernels_sw_emulation.PNG)  
2. Click on the hammer icon to start the compilation process:  
![Missing Image:HammerCompile](images/mixing-c-rtl-kernels_hammer.PNG)  
>**NOTE**: You will use software emulation to validate the function of the host code and kernel. Software emulation runtime is very fast, because both the host code and the kernel code are compiled and run on the x86 processor of the development machine.
3. To run emulation, click on the run icon:  
![Missing Image:RunIcon](images/run_icon.PNG)  
When the application completes successfully, you will see `TEST WITH ONE KERNEL PASSED` in the Console window.

### Application Timeline review

Look at the Application Timeline generated during software Emulation. The Application Timeline collects and displays host and device events on a common timeline. You can use it to visualize the host events and the kernel running.
1. In the Assistant view, expand **Emulation-SW**, and then expand **mixed_c_rtl-Default**, as shown below.  
![Missing Image:Application Timeline 1](images/mixed_c_rtl_default_from_assistant.png)
2. Double-click **Application Timeline** to see host events, including creating the program and buffers.
3.	Under the **Device**->**Binary Container**, you will see a line called `Compute Unit krnl_vadd_1`.  Traverse along the timeline and zoom in to see where the compute unit `krnl_vadd_1` is running.  
![Missing Image:Application Timeline 1](images/mixing-c-rtl-kernels_timeline_one_kernel.PNG)  
After reviewing, close the Application Timeline window.  
>**NOTE:** A compute unit is an instantiation of the kernel on the FPGA.

## Creating an RTL Kernel

Now that you have successfully added and run a C++ kernel, you are going to add an RTL Kernel to the `prj_c_rtl` project. Then, you will build and run both the C++ and RTL kernels.  For the SDAccel environment, the source of a kernel can be any one of the supported languages:
* OpenCL
* C/C++
* RTL

As you will see, regardless of how the kernels were designed, the host code accesses the kernels through similar function calls.  

### Use the RTL Kernel Wizard

First, you will use the RTL Kernel Wizard to generate a kernel that will add a constant to an input vector. The RTL Kernel Wizard can automate some of the steps needed to package an RTL design into a compiled kernel object file (`.xo`) that can accessed by the SDAccel environment.

By default, the wizard creates a simple vector addition kernel, like the C++ kernel used in the first part of this tutorial. You will create and add this RTL Kernel to your project.  
>**IMPORTANT**: For the purposes of creating the RTL kernel in this tutorial, you will quickly walk through the steps without much detail. However, the RTL Kernel Wizard is discussed in great detail in the [Getting Started with RTL Kernels](./docs/getting-started-rtl-kernels/README.md) tutorial. In addition, complete details of the RTL Kernel Wizard can be found in the _SDAccel Environment User Guide_ ([UG1023](https://www.xilinx.com/cgi-bin/docs/rdoc?v=2018.3;d=ug1023-sdaccel-user-guide.pdf)).

##### Open the RTL Kernel Wizard

1.	Under the Xilinx menu, select **RTL Kernel Wizard**. This opens RTL Kernel Wizard, which starts with a Welcome page.
2. Click **Next**.
3. In the General Settings dialog box, keep all the default settings, and click **Next**.
4. In the Scalars dialog box, set the number of scalar arguments to `0`, and click **Next**.
5. In the Global Memory dialog box, keep all the default settings, and click **Next**.  
The Summary dialog box provides a summary of the RTL kernel settings, and includes a function prototype which conveys what a kernel call would look like as a C function.
6. Click **OK**.


### The Vivado project

At this point, the Vivado™ Design Suite will open with a project for the generated RTL code. The RTL code generated and configured by the RTL Kernel wizard corresponds to the `A = A + 1 function`.  You can navigate to review the source files, or even run RTL simulation.  However, for this tutorial, you will only generate the RTL Kernel.

1. In Flow Navigator, click **Generate RTL Kernel**, as shown below.  
![Missing Image:Flow Navigator](images/mixing-c-rtl-kernels_flow_navigator.png)  
2. In the Generate RTL Kernel dialog box, select the **Sources-only** kernel packaging option.
3. For Software Emulation Sources (optional), you can add a C++ model of the RTL kernel, which can be used in the SDAccel tool during software emulation. The C++ model must be coded by the design engineer. Typically, there is no C++ model available and Hardware Emulation is used to test the design.  
Since the RTL Wizard creates a C++ model of the VADD design, it can now be added.
4. Click `…` (the browser button).
5. Double-click the `imports` directory.
6. Select the only `.cpp` file, and then click **OK**.
7. Click **OK** again to generate the RTL kernel.
8. After the RTL kernel has been generated successfully, click **Yes** to exit the Vivado Design Suite, and return to the SDAccel environment.

## Adding RTL and C++ Kernels to the Host Code

In the SDAccel environment, you will be returned to the `rtl_c_prj` project that you started from. Expand the `src` directory the Project Explorer, and you will see that the RTL kernel has been added to the project under `sdx_rtl_kernel`.

The kernel source folder contains two files:
- `host_example.cpp` (sample host code)
- `.xo` file (RTL Kernel)  
![Missing Image:KernelDir](images/mixing-c-rtl-kernels_kernel_dir_structure.PNG)

Since the project already includes the host code, you must delete the generated `host_example.cpp` file.  To do this right-click the `host_example.cpp` file, and select **delete**.

### Add New Kernel to Binary Container

With the new kernel added to the project, you must now add it to the binary container.

1. In the SDx Application Project Settings window, from the Hardware Functions area, click on the lightning bolt icon.
2. Select the RTL kernel that you just created, and then click **OK**.  
This adds the RTL kernel to the binary container (which already contains the C++ kernel).
3. Update the host code (`host.cpp`) to incorporate the new kernel, since the updates have already been done.
4. Open the `host.cpp` file, and remove the comment `//` on line 49 to define `ADD_RTL_KERNEL`:
  * Before:
  ```
  //#define ADD_RTL_KERNEL
  ```
  * After:
  ```
  #define ADD_RTL_KERNEL
  ```

5. Save the file.

  The updated `host.cpp` file includes additional OpenCL calls in the host code, as follows. The actual OpenCL calls are identical to the ones used for the C++ based kernel, with the arguments changed for the RTL-based kernel.  
  Though the first set of code was not changed, the binary container now contains _both_ the C++ and RTL kernels.
  ```
  cl::Program::Binaries bins;
  bins.push_back({buf,nb});
  devices.resize(1);
  cl::Program program(context, devices, bins);
  ```
6. Get the `sdx_kernel_wizard_0` object from the program, and assign the name `krnl_const_add` on line 145. The `sdx_kernel_wizard_0` object name will match the name generated with the RTL Wizard.
```
cl::Kernel krnl_const_add(program,"sdx_kernel_wizard_0");
```
7. Define the `krnl_const_add` kernel arguments on line 168. Important to note that in the host code, the buffer 'buffer_results' is passed directly from the C kernel to the RTL kernel via DDR without being moved back to the host memory.
```
krnl_const_add.setArg(0,buffer_result);
```
>**NOTE**: In the host code, the `buffer_results` buffer is passed directly from the C kernel to the RTL kernel via DDR without being moved back to the host memory.
8. Launch the `krnl_const_add` kernel on line 173:
```
q.enqueueTask(krnl_vector_add);
```  
With the RTL kernel added to the binary container and the host code, you can rebuild the project and run hardware emulation.

9. Ensure the active build configuration is set to **Emulation-HW**, then click on the run button, as shown below. This compiles and runs software emulation.  
![Missing Image:RunButton](images/mixing-c-rtl-kernels_run_button.PNG)  
You will see `TEST WITH TWO KERNELS PASSED` in the Console window when emulation completes.  
Open the Application Timeline report once more to see that the two kernels are running.

10. In the Assistant window, expand **Emulation-HW** and the default kernel, and then double-click on the Application Timeline file to open it.  
6.	Under **Device**->**Binary Container**, traverse along the timeline and zoom in. You will now see both compute units `krnl_vadd_1` and `rtl_kernel` running.  
![Missing Image:Application Timeline 2](images/mixing-c-rtl-kernels_timeline_two_kernels.PNG)

## Conclusion

This tutorial has demonstrated how SDAccel environment applications can use any combination of kernels, regardless of the language they were developed in. Specifically, we showed C++ and RTL based kernels running in the same application. The host code accesses and runs the kernels in an identical manner.

<hr/>
<p align="center"><sup>Copyright&copy; 2018 Xilinx</sup></p>
