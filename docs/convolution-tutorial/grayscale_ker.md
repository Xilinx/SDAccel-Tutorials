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

# 8. Adding Additional Kernel Processing

In the previous labs, you have been working with a single kernel, `convolve_fpga`, although you have implemented multiple instances of the kernel or CUs. The previous labs were all about optimizing the kernel to achieve your performance target. In this lab, you are going to add a second kernel to perform a post-processing step to convert the color image into a grayscale image, before converting back to video. This simply demonstrates the opportunity to accelerate multiple functions from your application onto the Alveo accelerator card.

Grayscale is an element-wise function, so it will be much simpler to optimize. Just like before, you are going to split the kernel into read, compute, and write tasks. However, you do not need to create three separate functions for this because there is no conditional logic between the loops.

## Kernel Code Modification

First, work on the kernel code.

1. In the `src/addi_k` folder, copy the `grayscale_kernel.cpp` file to a new file named `grayscale_fpga.cpp`.

2. Open the `grayscale_fpga.cpp` file in an editor, rename the `grayscale_cpu` function to `grayscale_fpga`, and save the file.

3. Add the fixed point and `hls_stream` headers to the top of the file.

        #include "ap_fixed.h"
        #include "hls_stream.h"

4. Just like before, create a fixed point type for the calculation.

        typedef ap_fixed<16,9> fixed;

5. Add the following pragmas below the function signature, inside the braces, to setup the interface of the kernel.

        #pragma HLS INTERFACE s_axilite port=return bundle=control
        #pragma HLS INTERFACE s_axilite port=inFrame bundle=control
        #pragma HLS INTERFACE s_axilite port=outFrame bundle=control
        #pragma HLS INTERFACE s_axilite port=img_width bundle=control
        #pragma HLS INTERFACE s_axilite port=img_height bundle=control
        #pragma HLS INTERFACE m_axi port=inFrame bundle=gmem1
        #pragma HLS INTERFACE m_axi port=outFrame bundle=gmem2
        #pragma HLS data_pack variable=inFrame
        #pragma HLS data_pack variable=outFrame

6. The grayscale conversion multiplies each of the color components of the pixel with a set of constant values. Because you are going to be using fixed point math, create three fixed type variables for each of the scalar values.

        fixed red = 0.30f;
        fixed green = 0.59f;
        fixed blue = 0.11f;

7. You must also create two data streams to transfer data between dataflow modules.

        hls::stream<RGBPixel> read("read");
        hls::stream<GrayPixel> write("write");

        int elements = img_height * img_width;

8. Now, replace the CPU calculation with three loops. One for reading from global memory, one for the computation, and another to write to global memory. Replace the following code.

        GrayPixel gray = (inFrame[i].r * 0.30) + //red
                         (inFrame[i].g * 0.59) + // green
                         (inFrame[i].b * 0.11);  // blue
        outFrame[i] = gray;

   with:

        #pragma HLS dataflow
        for(int i = 0; i < elements; ++i) {
            read << inFrame[i];
        }
        for (int i = 0; i < elements; ++i)
        {
            RGBPixel pixel;
            read >> pixel;
            GrayPixel gray = pixel.r * red + pixel.g * green + pixel.b * blue;
            write << gray;
        }
        for(int i = 0; i < elements; ++i) {
            write >> outFrame[i];
        }

Then, modify the `convolve.cpp` file to launch the `grayscale_fpga` kernel. Because you are adding this kernel into the same container as the convolution kernel, you only need to create a `cl::Kernel` object instead of reading a separate container.  

## Host Code Modification

Below are listed all the required changes on host code side, but you can simply define the GRAY_KERNEL_EN macro in the `convolve.cpp` file to enable the modifications.

1. Open the `convolve.cpp` file, and add a vector for the result of the grayscale conversion at the top of the `convolve` function.

        vector<GrayPixel, aligned_allocator<GrayPixel>> grayFrame(width * height);

2. The result of the convolution kernel will need to be read by the grayscale kernel. Previously, you were only writing the `buffer_output` `cl::Buffer`, so you passed in the `CL_MEM_WRITE_ONLY` flag to its constructor. Change this flag to `CL_MEM_READ_WRITE`, so that the new kernel can read from it. Change the following call from:

        cl::Buffer buffer_output(context, CL_MEM_WRITE_ONLY, frame_bytes, NULL);

      to:

        cl::Buffer buffer_output(context, CL_MEM_READ_WRITE, frame_bytes, NULL);

3. You also need to create an additional buffer to hold the result of the grayscale function. Add this line after the other buffer declarations.

        size_t gray_frame_bytes = width * height * sizeof(GrayPixel);
        cl::Buffer gray_output(context, CL_MEM_WRITE_ONLY, gray_frame_bytes, NULL);

4. After the `enqueueMigrateMemObjects` call, create a `cl::Kernel` object to represent the grayscale kernel in the container.

        cl::Kernel grayscale_kernel(program, "grayscale_fpga");

5. Next, set the kernel arguments for the grayscale kernel.

        grayscale_kernel.setArg(0, buffer_output);
        grayscale_kernel.setArg(1, gray_output);
        grayscale_kernel.setArg(2, width);
        grayscale_kernel.setArg(3, height);

6. When the `gray` parameter is set to TRUE, you want the host program to run the grayscale kernel. After all of the tasks for the `convolve_kernel` have been enqueued, add the following lines of code.

        if (gray) {
            cl::Event gray_event;
            q.enqueueTask(grayscale_kernel, &task_events, &gray_event);
            iteration_events.push_back(gray_event);
            cl::Event read_event;
            q.enqueueReadBuffer(gray_output, CL_FALSE, 0, gray_frame_bytes, grayFrame.data(), &iteration_events, &read_event); iteration_events.push_back(read_event);

            iteration_events.back().wait();
            bytes_written = fwrite(grayFrame.data(), 1, gray_frame_bytes, streamOut);
            fflush(streamOut);

            if(bytes_written != gray_frame_bytes) {
                printf("\nError: partial frame.\nExpected %zu\nActual %zu\n", gray_frame_bytes, bytes_written);
                break;
            }

            } else {
                cl::Event read_event;
                q.enqueueReadBuffer(buffer_output, CL_FALSE, 0, frame_bytes, outFrame.data(), &iteration_events, &read_event); iteration_events.push_back(read_event);

                iteration_events.back().wait();
                bytes_written = fwrite(outFrame.data(), 1, frame_bytes, streamOut);
                fflush(streamOut);

                if(bytes_written != frame_bytes) {
                       printf("\nError: partial frame.\nExpected %zu\nActual %zu\n", frame_bytes, bytes_written);
                    break;
                }

    If `gray` is set to TRUE, the host program enqueues the grayscale kernel. The grayscale kernel needs to wait for the tasks in the `task_events` call because these are the events associated with the `convolve_kernel` task. Afterwards, you are going to read from the `gray_output` buffer and write to the `grayFrame` function, which will be transferred to the output stream.

    The rest of the code handles the case where `gray` is false, and the new kernel is not enqueued.

## Run Hardware Emulation

Now you are ready to compile and run the design. Go to the `Makefile` directory, and use the following command to run hardware emulation.

   ```
   make run TARGET=hw_emu STEP=addi_k FAST_MODE=1 NUM_FRAMES=1
   ```

   >**NOTE:** Before compilation, you need to edit the `Makefile` to change the ADDI_KRNL variable from `FALSE` to `TRUE`.

## Conclusion

Congratulations! You have successfully completed all the modules of this lab.

1. You created an SDAccel application from a basic C application.
1. You familiarized yourself with the reports generated during software and hardware emulation.
1. You explored various methods of optimizing your HLS kernel.
1. You learned how to set an OpenCL API command queue to execute out-of-order for improved performance.
1. You enabled your kernel to run on multiple CUs.
1. You used the HLS dataflow directive, and explored how it affected your application.
1. You stacked multiple kernels to do additional processing.

</br>
<hr/>
<p align="center"><b><a href="/docs/sdaccel-getting-started/">Return to Getting Started Pathway</a> — <a href="./README.md">Return to Start of Tutorial</a></b></p>

<p align="center"><sup>Copyright&copy; 2019 Xilinx</sup></p>