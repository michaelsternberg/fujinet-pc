FujiNet-PC   
===========

Work in progress [FujiNet firmware](https://github.com/FujiNetWIFI/fujinet-platformio) port to Linux, macOS and Windows

**If your are interested into FujiNet - A multi-function peripheral built on ESP32 hardware being developed for the Atari 8-bit systems - please visit the project at GitHub: https://github.com/FujiNetWIFI**

-------------------------------------------------------------------

**Warning:** FujiNet-PC is still work in progress with ported components in various state of completeness and erroneousness...

-------------------------------------------------------------------

## Port Status

### Working

- Disk drive (D:) emulation with support for ATR disk images and XEX files (no ATX yet)
- Modem emulation (R:)
- Printer emulation (P:)
- APETIME protocol
- TNFS File System to access image files over network
- Web interface to control program's settings, browse TNFS hosts and mount disk images
- FujiNet network device (N:) with support for various network protocols:
  TCP, UDP, TNFS, HTTP, SMB, FTP, Telnet, SSH
- CP/M emulation
- Compiles and runs on Linux, macOS and Windows
- Binary packages for selected platforms

### Not (yet) working

- SAM voice synthesizer
- MIDIMaze support
- Program recorder (tape) emulation

-------------------------------------------------------------------

## Download

FujiNet-PC pre-compiled binaries for selected operating systems are available in [Releases](https://github.com/FujiNetWIFI/fujinet-pc/releases) section.

If interested into running FujiNet-PC with Altirra take a look at [FujiNet-PC Launcher](https://github.com/a8jan/fujinet-pc-launcher). The provided bundle includes FujiNet-PC binaries, Launcher GUI, NetSIO hub and NetSIO custom device for Altirra. An alternative to all in one bundle is a variant which includes scripts only (no binaries). Python 3 is needed to run scripts.

## Build instructions

The build process is controlled with [CMake](https://cmake.org/). Build works with GNU Make ~~or Ninja build systems~~, with GCC or Clang/LLVM C and C++ compilers.

### Build tools

The steps to get ready for building on **macOS** are described [here](macOS.md) and for **Windows** [here](Windows.md).

To install necessary build tools on **Debian**, **Ubuntu** and derivatives:

```sh
sudo apt install cmake g++
```

### Dependencies

Install necessary build libraries.

#### Debian/Ubuntu

```sh
sudo apt install libexpat-dev libmbedtls-dev
```

#### macOS

```sh
brew install mbedtls
```

#### Windows MSYS2 CLANG64

```sh
pacman -S clang64/mingw-w64-clang-x86_64-mbedtls clang64/mingw-w64-clang-x86_64-python-jinja clang64/mingw-w64-clang-x86_64-python-yaml
```

#### Python packages (all platforms)

Install Python packages for scripts used for build.

```sh
python -m pip install -U Jinja2 pyyaml
```

### Build

#### Linux and macOS

```sh
# get the source code
git clone https://github.com/FujiNetWIFI/fujinet-pc.git

# enter build directory
cd fujinet-pc/build

# prepare build
cmake .. -DCMAKE_BUILD_TYPE:STRING=Debug

# run build
cmake --build .

# after successful build populate dist directory with necessary files
cmake --build . --target dist
```



#### Windows

To build on Windows use MSYS2/CLANG64 environment. Start **CLANG64** shell (clang64.exe).

```sh
# get the source code
git clone https://github.com/FujiNetWIFI/fujinet-pc.git

# enter build directory
cd fujinet-pc/build

# prepare build
cmake .. -DCMAKE_BUILD_TYPE:STRING=Debug -G "MSYS Makefiles"

# run build
cmake --build .

# after successful build populate dist directory with necessary files
cmake --build . --target dist
```

#### SD Card

FN-PC uses SD folder, not real SD Card. Visit [FujiNet SD Card](https://github.com/FujiNetWIFI/fujinet-sd-card) to get some useful utilities which can be placed into SD folder.


## Run it

`dist` directory (build/dist) contains files needed to run FujiNet-PC. You can run fujinet directly inside `dist` or copy/move/rename the `dist` directory to the place of your preference and run fujinet from there.

```sh
# enter dist directory (or copied/moved/renamed directory, if you copied/moved/...), must be inside
cd dist

# optionally, put some additional disk image(s) to SD sub-directory
cp /your/dir/some/image.atr SD

# start fujinet with wrapper script
./run-fujinet  # or run-fujinet.bat from Windows command prompt
```

It can be stopped with `Ctrl`+`C`

### Configure

Visit http://localhost:8000 and configure Serial Port to communicate with real Atari or enable SIO over Network for communication with Altirra Atari emulator.

For emulator options check instructions [here](https://github.com/FujiNetWIFI/fujinet-emulator-bridge). Alternatively, [FujiNet-PC Launcher](https://github.com/a8jan/fujinet-pc-launcher) can be used.

Connect SIO2PC/USB and boot the Atari from FujiNet.

By default FujiNet Web Interface is available on port 8000 listening on all available IP addresses. This can be changed with `-u <URL>` parameter. For example:

```sh
# to limit the web interface only for machine which is running fujinet 
# and to listen on non-default port 9001
./run-fujinet -u http://localhost:9001

# "http://" part can be omitted
# this will make web interface available on any address assigned to PC/Mac/RPi
# port for web interface will be 8080
./run-fujinet -u 0.0.0.0:8080
```
