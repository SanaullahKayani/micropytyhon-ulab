### Run Micropython-ulab on ESP32-based boards

`ulab` can be tested on the ESP32 in [wokwi's micropython emulator](https://wokwi.com/arduino/projects/322114140704342610) without having to compile the C code. This utility also offers the possibility to save and share your `micropython` code.

Firmware for `Espressif` hardware can be built in two different ways, which are discussed in the next two paragraphs. A solution for issues with the firmware size is outlined in the [last paragraph](#what-to-do-if-the-firmware-is-too-large) of this section.

#### Compiling with cmake

Beginning with version 1.15, `micropython` switched to `cmake` on the ESP32 port. If your operating system supports `CMake > 3.12`, you can either simply download, and run the single [build script](https://github.com/v923z/micropython-ulab/blob/master/build/esp32-cmake.sh), or follow the step in this section. Otherwise, you should skip to the [next one](#compiling-with-make), where the old, `make`-based approach is discussed.

In case you encounter difficulties during the build process, you can consult the (general instructions for the ESP32)[https://github.com/micropython/micropython/tree/master/ports/esp32#micropython-port-to-the-esp32].

First, clone the `ulab`, the `micropython`, as well as the `espressif` repositories:

```bash
export BUILD_DIR=$(pwd)

git clone https://github.com/v923z/micropython-ulab.git ulab
git clone https://github.com/micropython/micropython.git

cd $BUILD_DIR/micropython/

git clone -b v4.0.2 --recursive https://github.com/espressif/esp-idf.git

```
Also later releases of `esp-idf` are possible (e.g. `v4.2.1`).

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
At this point, all requirements are installed and built. We can now compile the firmware with `ulab`. In `$BUILD_DIR/micropython/ports/esp32` create a `makefile` with the following content:

```bash
BOARD = GENERIC
USER_C_MODULES = $(BUILD_DIR)/ulab/code/micropython.cmake

include Makefile
```
You specify with the `BOARD` variable, what you want to compile for, a generic board, or `TINYPICO` (for `micropython` version >1.1.5, use `UM_TINYPICO`), etc. Still in `$BUILD_DIR/micropython/ports/esp32`, you can now run `make`.
