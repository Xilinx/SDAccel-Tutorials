XOCC := $(XILINX_SDX)/bin/xocc
EMCONFIGUTIL := $(XILINX_SDX)/bin/emconfigutil
MODE := hw_emu
DSA := xilinx_u200_xdma_201830_1
NKERNEL := Filter2DKernel:1

# sources
HOST_SRC := src/host/*.cpp
KERNEL_XO := src/kernel/Filter2DKernel.xo

# targets
HOST_EXE := filter2d.exe
XCLBIN := filter2d.$(MODE).xclbin
EMCONFIG_FILE := emconfig.json

# XOCC Linker options to configure number of compute units
XOCC_LINK_OPTS := --nk ${NKERNEL}

# XOCC Compiler options
XOCC_COMMON_OPTS := -s -t $(MODE) --platform $(DSA)

CFLAGS := -g -std=c++11 -I$(XILINX_XRT)/include -I${XILINX_SDX}/include
LFLAGS := -L$(XILINX_XRT)/lib -lxilinxopencl -lrt -fopenmp -Wl,--as-needed -Wl,-rpath,${XILINX_SDX}/lnx64/tools/opencv -L${XILINX_SDX}/lnx64/tools/opencv -lopencv_core -lopencv_highgui


NUMDEVICES := 1

# run time args
EXE_OPT := -x filter2d.${MODE}.xclbin -i ./img/test.bmp -n 1

# primary build targets
.PHONY: xclbin app all

xclbin:  $(XCLBIN)
app: $(HOST_EXE) 

all: xclbin app

clean:
	-$(RM) $(EMCONFIG_FILE) $(HOST_EXE) $(XCLBIN) 

$(XCLBIN): $(KERNEL_XO)
	$(XOCC) $(XOCC_COMMON_OPTS) -l -o $@ $+ $(XOCC_LINK_OPTS)

# host rules
$(HOST_EXE): $(HOST_SRC)
	xcpp $(CFLAGS) -o $@ $+ $(LFLAGS)
	@echo 'Compiled Host Executable: $(HOST_EXE)'

$(EMCONFIG_FILE):
	$(EMCONFIGUTIL) --nd $(NUMDEVICES) --od . --platform $(DSA)

check: $(XCLBIN) $(HOST_EXE) $(EMCONFIG_FILE)
	XCL_EMULATION_MODE=${MODE} ./$(HOST_EXE) $(EXE_OPT)
