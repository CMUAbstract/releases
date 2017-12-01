# Software+Hardware Releases by Abstract Group at Carnegie Mellon University

This wiki-like bootstrap repository contains the getting-started information
for developers who would like to make use of the software and hardware tools we
release. For projects that span multiple repositories (SW, HW, example
applications, etc), this repository is the starting point with guides and
structured links to the repositories within the flat repository pool hosted on
GitHub. Follow the link for each project to see a synopsis and a quick-start
guide.

## Programming models for intermittent computing

* [Alpaca](Alpaca.md) \[[web](https://cmuabstract.github.io/alpaca-landing-page/)\] :
a checkpoint-less programming model with compiler support for intermittent programming
* [Chain](Chain.md): a task-based programming model and runtime for intermittent programming
* [Capybara](Capybara.md): hardware and firmware for reconfigurable energy storage

* Prior work **by other researchers**, forked, updated, and re-packaged for
  seemless builds of applications alongside builds with our systems:

    - [DINO](DINO.md) : static checkpoints with compiler-analysis that versions non-volatile data
    - [Mementos](Mementos.md) : statically-inserted dynamic checkpoints

## Tools

* [EDB](EDB.md): an energy-interference-free debugger for energy-harvesting devices

## Case-studies

* [EDBsat](EDBsat.md): a solar-powered nano-satellite for sensing with a built-in profiler

## Benchmark applications

The applications in the following table target intermittently-powered
energy-harvesting devices and perform sensing with various computation typical
of the embedded domain. Each application was tested on at least one real
energy-harvesting device ([WISP5](http://wisp5.wikispaces.com/WISP+Home),
[Capybara](Capybara.md), or TI Launchpad powered by a
[PowerCast RF
harvester](http://www.powercastco.com/products/powerharvester-receivers/)) on
intermittent power. So far, all devices are based on MSP430.

All applications are built using [Maker](https://github.com/CMUAbstract/maker)
dependency build system. The application is the root of the dependency tree,
and its dependencies (libraries and
[toolchains](#programming-models-and-runtimes-for-intermittently-powered-hardware))
are specified in the application\'s Maker makefile.  Toolchain dependencies
provide the compiler passes and runtime libraries needed to build the app using
that toolchain, along with build recipes for Maker that specify how to invoke
compiler passes, link the runtime. When Maker builds an
application, it builds all its dependencies from source, to ensure that all
compiler flags are the same. Builds using multiple toolchains coexist in
separate subdirectories of `bld/`.

Each application repository includes references to the repositories of its
dependencies as Git submodules (in `ext/`), so clone with `git clone
--recursive ...`. This makes it easy for each application to depend on a
snapshot of each dependency (including toolchains), while the dependency
continues to evolve (along with other applications) without breaking this
application. Dependencies that are exceptions to this rule are TI GCC
compiler, and LLVM/Clang compiler, because we don\'t normally modify them,
in contrast to our toolchains. This does mean, however, that the
version of the compilers, is not enforced via the submodule snapshot, so last
known working version of the compiler should be recorded in the documentation
somewhere.

For toolchains that provide significantly different programming models (e.g., C
code with boundaries vs\. task-based code), applications have multiple
implementations, one per toolchain.  The implementations are stored either as
branches in the same repository or in separate repositories.

| Name    | Description  | C/Mementos/DINO | Chain | Alpaca |
| ------- | ------------ | ------ | ----- | ------ |
| Blinker | blink an LED | [repo](https://github.com/CMUAbstract/app-blinker) | [repo](https://github.com/CMUAbstract/app-blinker-chain) | |
| SpaceData | sense for [EDBsat](EDBsat.md) | | [repo](https://github.com/CMUAbstract/app-space-data-chain) | |
| AR | activity recognition using trained model | [repo](https://github.com/CMUAbstract/app-activity-chkpt) | [repo](https://github.com/CMUAbstract/app-activity-chain) | [repo](https://github.com/CMUAbstract/alpaca-activity) |
| RSA | public-key encryption | | [repo](https://github.com/CMUAbstract/app-rsa-chain) | [repo](https://github.com/CMUAbstract/alpaca-rsa) |
| Cuckoo | cuckoo filter data structure | | [repo](https://github.com/CMUAbstract/app-cuckoo-chain) | [repo](https://github.com/CMUAbstract/alpaca-cuckoo) |
| CEM | cold-chain equipment monitor with LZW compression | | [repo](https://github.com/CMUAbstract/app-temp-log-chain) | [repo](https://github.com/CMUAbstract/alpaca-temp-log) |
| Bitcount | counts bits in bitmask | [repo](https://github.com/CMUAbstract/dino_bitcount) | [repo](https://github.com/CMUAbstract/chain-bitcount) | [repo](https://github.com/CMUAbstract/alpaca-bitcount) |
| Blowfish | encrypt with Blowfish algorithm | [repo](https://github.com/CMUAbstract/dino_blowfish) | [repo](https://github.com/CMUAbstract/chain-blowfish) | [repo](https://github.com/CMUAbstract/alpaca-blowfish) |
| Gesture | recognize gesture and send over BLE | | [repo](https://github.com/CMUAbstract/app-gesture-chain) | |
| TempAlarm | monitor temperature and send BLE alarm | | [repo](https://github.com/CMUAbstract/app-tempalarm-chain) | |

## Misc drivers and libraries

* [libmsp](https://github.com/CMUAbstract/libmsp) : basic low-level drivers and
  macros for accessing peripherals on MSP430 (watchdog, GPIO, UART) and a small
  [printf implementation](http://forum.43oh.com/topic/1289-tiny-printf-c-version/#entry10652)
* [libio](https://github.com/CMUAbstract/libio) : provides a console with printing
  and logging to a selectable backend (HW UART from `libmsp`, SW UART from
  `msp430-softuart`, [EDB](EDB.md))
* [libharvest](https://github.com/CMUAbstract/libharvest) : minimum
  functionality required to run on an energy-harvester without specialized HW
  (sleep until threshold using internal comparator on MSP430)
* [libmcppot](https://github.com/CMUAbstract/libmcppot) : driver for MCP4xxx
  digital potentiometer for I2C on MSP430
* [libfxl](https://github.com/CMUAbstract/libfxl) : driver for FXL64xx
  GPIO expander for I2C on MSP430
* [libtemp](https://github.com/CMUAbstract/libtemp) : driver for TMP36 analog
  temperature sensor using MSP430 ADC
* [libmspuartlink](https://github.com/CMUAbstract/libmspuartlink) : library
  for sending packets across UART between two MSP430 MCUs

* Forks of libraries by **other authors** re-packaged for [Maker](https://github.com/CMUAbstract/maker):

  - [msp430-softuart](https://github.com/CMUAbstract/msp430-softuart) : SW (bitbanged) UART
  implementation for MSP430 by [Nicholas J.  Conn](https://github.com/wendlers/msp430-softuart)
  - [libmspware](https://github.com/CMUAbstract/libmspware) : [TI
    MSPware](http://www.ti.com/tool/mspware) library with peripheral drivers for MSP430

## Misc utilities

* [Maker](https://github.com/CMUAbstract/maker) : dependency build system for C projects,
  implemented in GNU Make, that automatically builds dependencies along with the
  top-level application
* [mspdma](https://github.com/CMUAbstract/mspdma) : measure DMA performance on MSP430

## Misc packages

* [fftw-PKGBUILD](https://github.com/CMUAbstract/fftw-PKGBUILD) : Arch Linux
  PKGBUILD for FFTW library that enables NEON SIMD on ARM
