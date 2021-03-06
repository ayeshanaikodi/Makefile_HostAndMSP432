
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
CCSIZE = size
CCSIZEFLAGS = -Atd
LD = ld
LDFLAGS = -Wl,-Map=$(TARGET).map
OBJDUMP = objdump
OBJDFLAGS = -S
CFLAGS = -std=$(STD)	\
	-Wall	\
	-Werror	\
	-g	\
	-O0
CPPFLAGs = -E
endif

ifeq ($(PLATFORM),MSP432)
CC = arm-none-eabi-gcc
CCSIZE = arm-none-eabi-size
CCSIZEFLAGS = -Atd
LD = arm-none-eabi-ld
OBJDUMP = arm-none-eabi-objdump
OBJDFLAGS = -S
LDFLAGS = -Wl,-T $(LINKER_FILE)	\
	-Wl,-Map=$(TARGET).map
CFLAGS = -Werror	\
 	-O0	\
	-mcpu=$(CPU)	\
	-march=$(ARCH)	\
	-mfloat-abi=$(FLOAT_ABI)	\
	-mfpu=$(FPU)	\
	--specs=$(SPECS)	\
	-std=$(STD)	\
	-Wall
CPPFLAGS = -E
endif

OBJECTS = $(SOURCES:.c=.o)
DEPENDENCIES = $(SOURCES:.c=.d)
#ASSMS = $(SOURCES:.c=.asm)
#PREPRSD = $(SOURCES:.c=.i)


%.o : %.c
	$(CC) -c $^ $(CFLAGS) -D$(PLATFORM) $(INCLUDES) -MD -o $@

#%.d : %.c
#	$(CC) -c $^ $(CFLAGS) -D$(PLATFORM) $(INCLUDES) -MM -MF $@

%.i : %.c
	$(CC) -c $^ $(CFLAGS) $(INCLUDES) -D$(PLATFORM) $(CPPFLAGS) -o $@

%.asm : %.o
	$(OBJDUMP) $(OBJDFLAGS) $^ > $@

$(TARGET).asm : $(TARGET).out
	$(OBJDUMP) $(OBJDFLAGS) $^ > $@


.PHONY: compile-all
compile-all: $(OBJECTS)

.PHONY: build
build: $(TARGET).out


$(TARGET).out: $(OBJECTS) #$(DEPENDENCIES)
	$(CC) $^ $(CFLAGS) $(LDFLAGS) -D$(PLATFORM) $(INCLUDES) -o $@
	$(CCSIZE) $(CCSIZEFLAGS) $(TARGET).out


.PHONY: clean
clean:
	rm -f $(TARGET).out $(TARGET).map *.o *.asm *.i *.d
