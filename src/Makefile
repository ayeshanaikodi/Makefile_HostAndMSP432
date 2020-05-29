
#bash regenerate-makefiles.sh
#******************************************************************************
# Copyright (C) 2017 by Alex Fosdick - University of Colorado
#
# Redistribution, modification or use of this software in source or binary
# forms is permitted as long as the files maintain this copyright. Users are
# permitted to modify this and use it to learn about the field of embedded
# software. Alex Fosdick and the University of Colorado are not liable for any
# misuse of this material.
#
#*****************************************************************************

#------------------------------------------------------------------------------
# Makefile to support 2 platforms: Host and MSP432
#
# Use: make [TARGET] [PLATFORM-OVERRIDES]
#
# Build Targets:
#      <FILE>.o - Builds <FILE>.o object file
#      build - Builds and links all source files
#      clean - removes all generated files
#
# Platform Overrides:
#      PLATFORM - Target platform (HOST, MSP432)
#
#------------------------------------------------------------------------------
include sources.mk

# Platform Overrides
PLATFORM = HOST

STD = c99

# Architectures Specific Flags
LINKER_FILE = ../msp432p401r.lds
CPU = cortex-m4
ARCH = armv7e-m
FLOAT_ABI = hard
FPU = fpv4-sp-d16
SPECS = nosys.specs

TARGET = c1m2

# Compiler Flags and Defines
ifeq ($(PLATFORM),HOST)
CC = gcc
LD = ld
CFLAGS = -std=$(STD) -Wall -g
CPPFLAGs = -E #Stops at the preprocessing step
endif

ifeq ($(PLATFORM),MSP432)
CC = arm-none-eabi-gcc
LD = arm-none-eabi-ld
LDFLAGS = -Wl,-T $(LINKER_FILE) -Wl,-Map=$(TARGET).map
CFLAGS = -mcpu=$(CPU) -march=$(ARCH) -mfloat-abi=$(FLOAT_ABI) -mfpu=$(FPU) --specs=$(SPECS) -std=$(STD) -Wall
CPPFLAGs = -E #Stops at the preprocessing step
endif

OBJECTS = $(SOURCES:.c=.o)

%.o : %.c
	$(CC) -c $^ $(CFLAGS) -D$(PLATFORM) $(INCLUDES) -o $@

%.i : %.c
	$(CC) -c $^ $(CFLAGS) -D$(PLATFORM) -E $(INCLUDES) -o $@

.PHONY: compile-all
build:


.PHONY: build
build: $(TARGET).out

$(TARGET).out: $(OBJECTS)
	$(CC) $^ $(CFLAGS) $(LDFLAGS) -D$(PLATFORM) $(INCLUDES) -o $@

.PHONY: clean
clean:
	rm -f $(TARGET).out $(TARGET).map $(SOURCES:.c=.o)        # $(OBJECTS) wasn't working for some weird reason \_(-.-)_/
