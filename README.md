### Run Micropython-ulab on ESP32-based boards in Linux Operating System

#### Prerequisite/Requirement of Micropython-ulab
You must have installed cmake and ESP-idf in you PC. You can download the ESP-idf from https://docs.espressif.com/projects/esp-idf/en/stable/esp32/get-started/index.html and camake from https://cmake.org/download/

It's recommended to download the latest stable version of cmake. However, you may face issues with ESP-IDF's latest version, so you can install any other version. 
Firmware for `Espressif` hardware can be built in two different ways,, you can use any of them.

#### Compiling with cmake

First, clone the `ulab`, the `micropython`, as well as the `espressif` repositories:

```bash
export BUILD_DIR=$(pwd)

git clone https://github.com/v923z/micropython-ulab.git ulab
git clone https://github.com/micropython/micropython.git

cd $BUILD_DIR/micropython/

git clone -b v5.1 --recursive https://github.com/espressif/esp-idf.git

```
Also later releases of `esp-idf` are possible (e.g. `v5.2`).

Then install the `ESP-IDF` tools:

```bash
cd esp-idf
./install.sh
. ./export.sh
```

Next, build the `micropython` cross-compiler, and the `ESP` sub-modules:

```bash
cd $BUILD_DIR/micropython/mpy-cross
make
cd $BUILD_DIR/micropython/ports/esp32
make submodules
```
After successfully install all the module, You have two options. Either you can run the build command inside your project or create a `makefile` and make it.

#### Option 1
go to `$BUILD_DIR/micropython/ports/esp32` and run 
```bash
idf.py build
```
This will generate a micropython image inside `$BUILD_DIR/micropython/ports/esp32/build-ESP32_GENERIC`. You can use that image to flash ESP-32

#### Option 2
Instead of build, you can also compile the firmware with `ulab`. In `$BUILD_DIR/micropython/ports/esp32` create a `makefile` with the following content:
```bash
BOARD = GENERIC
USER_C_MODULES = $(BUILD_DIR)/ulab/code/micropython.cmake

include Makefile
```
You specify with the `BOARD` variable, what you want to compile for, a generic board, or `TINYPICO` (for `micropython` version >1.1.5, use `UM_TINYPICO`), etc. Still in `$BUILD_DIR/micropython/ports/esp32`, you can now run `make`.

### Flash ESP-32 with Micropython Image
You can flash you ESP_32 by running the following command in your root directory.
```bash

$BUILD_DIR/micropython/esp-idf/components/esptool_py/esptool/esptool.py -p /dev/ttyUSB0 -b 460800 --before default_reset --after hard_reset --chip esp32  write_flash --flash_mode dio --flash_size 4MB --flash_freq 40m 0x1000 $BUILD_DIR/micropython/ports/esp32/build-ESP32_GENERIC/bootloader/bootloader.bin 0x8000 $BUILD_DIR/micropython/ports/esp32/build-ESP32_GENERIC/partition_table/partition-table.bin 0x10000 $BUILD_DIR/micropython/ports/esp32/build-ESP32_GENERIC/micropython.bin
```

### Run Micropython on ESP-32
Just open the terminal and run this command. /dev/ttyUSB0 is most common port used in Linux for ESP_32, you can change the port accordingly.
```bash
picocom -b 115200 /dev/ttyUSB0
```
