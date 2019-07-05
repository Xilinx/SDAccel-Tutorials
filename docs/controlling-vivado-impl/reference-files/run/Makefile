#######################################################################################
.PHONY: help
help:
	@echo "Makefile Usage:"
	@echo "  make build TARGET=<sw_emu/hw_emu/hw> PLATFORM=<FPGA platform>"
	@echo "      Command to generate the design for specified Target and Device."
	@echo ""
	@echo "  make run TARGET=<sw_emu/hw_emu/hw> PLATFORM=<FPGA platform>"
	@echo "      Command to run for specified Target."
	@echo ""
	@echo "  make exe "
	@echo "      Command to generate host."
	@echo ""
	@echo "  make xclbin "
	@echo "      Command to generate hardware platform files(xo,xclbin)."
	@echo ""
	@echo "  make clean "
	@echo "      Command to remove the generated files."
	@echo ""
#######################################################################################
TARGET := hw
PLATFORM := xilinx_u200_xdma_201830_1
HOST_EXE := host
XO := apply_watermark.$(TARGET).$(PLATFORM).xo
XCLBIN := apply_watermark.$(TARGET).$(PLATFORM).xclbin

# Host building global settings
CXXFLAGS := -I$(XILINX_XRT)/include/ -I$(XILINX_VIVADO)/include/ -Wall -O0 -g -std=c++14 -L$(XILINX_XRT)/lib/ -lpthread -lrt -lstdc++
CXXFLAGS2 := -lOpenCL 

# Kernel compiler & linker global settings
KRNL_COMPILE_OPTS := -t $(TARGET) --platform $(PLATFORM) -g --max_memory_ports apply_watermark
KRNL_LINK_OPTS := -t $(TARGET) --platform $(PLATFORM) -g --sp apply_watermark_1.m_axi_gmem0:DDR[0] --sp apply_watermark_1.m_axi_gmem1:DDR[1] -s --xp vivado_prop:run.my_rm_synth_1.{STEPS.SYNTH_DESIGN.ARGS.FLATTEN_HIERARCHY}={full} --xp vivado_prop:run.impl_1.{STEPS.ROUTE_DESIGN.ARGS.DIRECTIVE}={NoTimingRelaxation} -R2

build: $(HOST_EXE) $(XO) $(XCLBIN) emconfig

exe: $(HOST_EXE)

xclbin: $(XO) $(XCLBIN)

# Building kernel
$(XO): ../src/krnl_watermarking.cl
	xocc $(KRNL_COMPILE_OPTS) -c -k apply_watermark -o'$@' '$<'
$(XCLBIN): $(XO)
	xocc $(KRNL_LINK_OPTS) -l --nk apply_watermark:1:apply_watermark_1 -o'$@' $(+)

# Building Host
$(HOST_EXE): ../src/host.cpp
	xcpp $(CXXFLAGS) -o '$@' '$<' $(CXXFLAGS2)

.PHONY: emconfig
emconfig: 
	emconfigutil --platform $(PLATFORM)

# Cleaning stuff
.PHONY: clean
clean:
	rm -f $(HOST_EXE) *apply_watermark.*.$(PLATFORM).* *.log *.json *.xo output.bmp

# Please set XCL_EMULATION_MODE manually before running sw_emu/hw_emu mode
.PHONY: run
run: build
ifeq ($(TARGET),$(filter $(TARGET),sw_emu hw_emu))
	XCL_EMULATION_MODE=$(TARGET) ./$(HOST_EXE) $(XCLBIN) ../src/inputImage.bmp ../src/golden.bmp 
else
	./$(HOST_EXE) $(XCLBIN) ../src/inputImage.bmp ../src/golden.bmp
endif

