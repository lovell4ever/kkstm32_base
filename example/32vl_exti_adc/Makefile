TARGET=32vl_exti_adc
PLATFORM=stm32f10x
LINKER_BASENAME=stm32f100xb
# TODO - unify stmlib and platform....
VECTORS=stm32f100x
STMLIB=stm32_stdperiph_f10x
# all below here should not need to be touched...(much)


EXECUTABLE=$(TARGET).elf
BIN_IMAGE=$(TARGET).bin

CC=arm-none-eabi-gcc
OBJCOPY=arm-none-eabi-objcopy

DEBUG?=1
STM32_BASE=../..
STLINK_HOME=/home/karlp/src/stlink


CFLAGS=-Os -mlittle-endian -mthumb
CFLAGS+=-mcpu=cortex-m3
CFLAGS+=-ffreestanding -nostdlib -nostdinc
CFLAGS+=-Wall -Wextra
ifeq ($(DEBUG),1)
        CFLAGS+=-g
endif

BOOT_FLASH?=1
ifeq ($(BOOT_FLASH),1)
	CFLAGS+=-Wl,-T,$(LINKER_BASENAME)_flash.ld
else
	CFLAGS+=-Wl,-T,$(LINKER_BASENAME)_sram.ld
	CFLAGS+=-DCONFIG_BOOT_SRAM=1
	# This keeps the standard system_xxx files from doing any damage...
	CFLAGS+=-DVECT_TAB_SRAM
endif

LIBS_STM_PATH=$(STM32_BASE)/libs_stm

# stm32l_discovery lib
CFLAGS+=-I$(LIBS_STM_PATH)/inc/base
CFLAGS+=-I$(LIBS_STM_PATH)/inc/core_support
CFLAGS+=-I$(LIBS_STM_PATH)/inc/device_support
CFLAGS+=-I$(LIBS_STM_PATH)/inc/$(PLATFORM)

LDFLAGS+=-L$(LIBS_STM_PATH)/build -l$(STMLIB)
LDFLAGS+=-L$(STM32_BASE)/ld_scripts
LDFLAGS+=-Wl,-Map=$(TARGET).map

all: $(BIN_IMAGE)

$(BIN_IMAGE): $(EXECUTABLE)
	$(OBJCOPY) -O binary $^ $@

$(EXECUTABLE): main.c $(STM32_BASE)/startup/cm3_genstartup.c $(STM32_BASE)/startup/$(VECTORS)_vectors.c
	$(CC) $(CFLAGS) $^ -o $@ $(LDFLAGS)

clean:
	rm -rf $(EXECUTABLE)
	rm -rf $(BIN_IMAGE)

write: all
	$(STLINK_HOME)/flash/flash write blah $(BIN_IMAGE) 0x08000000

.PHONY: all clean write
