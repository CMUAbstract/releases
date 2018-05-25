Toolchains
==========

Most released software listed in this repository is packaged to be built with
the [Maker](https://github.com/CMUAbstract/maker) dependency build system.
Maker knows how to build code for MSP430 using two third-party toolchains
that you need to install into your system as described below.

This document also describes what is needed to build code for ARM
microcontrollers from TI, which is not supported by Maker as of now.

This document also describes what is needed to flash binaries onto the MSP430
and some tips and tricks related to energy-harvesting power supply.

MSP430
------

To build code for the MSP430 architecture (a microcontroller family from TI),
you need a compiler with accompanying header files and libraries with the C
runtime (collectively referred to as a *toolchain*). Most released software
listed in this repository is packaged to be built with the
[Maker](https://github.com/CMUAbstract/maker) dependency build system. Maker
knows how to use two toolchains to build code for MSP430: TI GCC and LLVM/Clang.
Note that the latter depends on the former, because Clang can only compile
code into assembly, which must then be assembled into object code by the
assembler shipped with TI GCC.

### TI GCC

The [TI GCC for MSP430](http://www.tool.com/mps430-gcc-opensourcemspms) is a
pre-built toolchain based on GCC, and released by TI. This toolchain is conveniently
packaged for Arch Linux by the
[mspgcc-ti](https://aur.archlinux.org/packages/mspgcc-ti) package on AUR. To
install on Arch Linux, either `yaourt ti-mspgcc` if you use the `yaourt` helper,
or manually:

    wget https://aur.archlinux.org/cgit/aur.git/snapshot/mspgcc-ti.tar.gz
    tar xf mspgcc-ti.tar.gz
    cd mspgcc-ti
    makepkg -si
    

On other distributions, there might be a package, otherwise install using the
installer from TI (might be hard to uninstall, as with all unpackaged
software).

On Arch Linux, the install location is `/opt/ti/mspgcc`. To point Maker to
the toolchain, set the (badly named) `TOOLCHAIN_ROOT` environment variable
to this path, or set this variable in `ext/maker/Makefile.env`.

TI GCC ships with linker scripts for each device, and Maker contains
(slight) forks of these linker scripts -- see Maker documentation for
details.

TI GCC generated ELF binaries that can be flashed to the microcontroller as
described [in the section on flashing](#flashing-binary-onto-microcontroller).

### LLVM/Clang

The [LLVM/Clang](http://clang.org/) toolchain is capable of comping C down to
MSP430 assembly (using LLVM's MSP430 backend). The assembly must them be
assembled into object code using TI GCC's assembler. Maker includes recipies
that do everything needed to generate the ELF binary. The same high-level Maker
build recipe defined in `bld/Makefile` is sufficient to build an application
with either TI GCC or LLVM/Clang.

The only Clang version that has been tested is 3.8 (Clang commit 52ed5ec631 and
LLVM commit be2b2c32d38) and 6.0 (Clang commit bfc08965af and LLVM commit
646a736e7b4). Both versions need a patch related to interrupt
service routines (ISRs): [patch for
3.8](clang-MSP430-accept-all-interrupt-vector-numbers-3-8.patch) or [patch for
6.0](clang-MSP430-accept-all-interrupt-vector-numbers-6-0.patch).

Since Clang needs to be patched, it needs to be installed from source.
Below is a summary of [the official installation
instructions](http://clang.llvm.org/get_started.html) and [CMake build
settings](http://llvm.org/docs/CMake.html).

    mkdir llvm && cd llvm

    git clone http://llvm.org/git/llvm.git llvm-src
    cd llvm-src
    git checkout -b v6.0 646a736e7b4
    cd tools
    git clone http://llvm.org/git/clang.git
    cd clang
    git checkout -b v6.0 bfc08965af

    wget https://github.com/CMUAbstract/releases/raw/master/clang-MSP430-accept-all-interrupt-vector-numbers-6-0.patch
    git am < clang-MSP430-accept-all-interrupt-vector-numbers-6-0.patch
    
    cd ../../..
    mkdir llvm-install && mkdir llvm-build
    cd llvm-build
    cmake -G "Unix Makefiles" -DLLVM_OPTIMIZED_TABLEGEN=1 -DLLVM_TARGETS_TO_BUILD="MSP430;X86" \
        -DCMAKE_LINKER=/usr/bin/ld.gold -DCMAKE_INSTALL_PREFIX=../llvm-install ../llvm-src
    make -j4
    make install

Setting linker to ld.gold prevents out-of-memory during linking that was
encountered with the default GNU ld.

To point Maker to this build of Clang, set `LLVM_ROOT` to absolute path of the
`llvm/llvm-install` directory, or set this variable in
`ext/maker/Makefile.env`.


#### Patch for Clang to fix interrupt service routines

The problem is that Clang either rejects the argument to `interrupt()` attribute,
that defines the interrupt vector number, or that Clang generates duplicate
symbols `__isr_`. The problem and the solution is described in detail below.
The patch can be found
[here for 3.8](clang-MSP430-accept-all-interrupt-vector-numbers-3-8.patch) and
[here for 6.0](clang-MSP430-accept-all-interrupt-vector-numbers-6-0.patch), and
applied
as was described below.

Note that [libmsp](https://github.com/CMUAbstract/libmsp) provides the `ISR()`
macro for defining ISRs that is portable across both TI GCC and Clang, provided
that the Clang is patched with the following patch. See documentation for ISR()
usage in `libmsp/src/include/libmsp/periph.h`.

Despite both GCC and Clang using the same linker script, for Clang, we need to
manually create a symbol and allocate it in the per-vector section (named with
a name that matches the names that the linker script expects). Note that Clang
creates `__isr_*` symbols automatically, but these are only interpreted by the
deprecated MSPGCC compiler/linker, not by TI GCC for MSP. See more on this
below.

Clang creates `__isr_*` symbols for MSPGCC (see lib/CodeGen/TargetInfo.cpp
MSP430TargetCodeGenInfo::SetTargetAttributes), these are not relevant for TI
GCC, but they are not entirely harmless. They cause a problem because Clang
generates `__isr_N` where N is divided by 2 (because MSPGCC wants it that way,
I guess). So, if we pass a vector number, the odd and even vector numbers will
generate the same symbol and therefore linking will fail with a
multiply-defined symbol error. As a workaround we pass the doubled value.

There is one more problem that so far requires a patch to Clang: it rejects
vector numbers that are odd or above 30. So, this problem with the above
problem form a catch-22 situation: if we pass `N/2` then the atttribute is
accepted, but we get multiply-defined values; if we pass `N`, it's rejected;
and if we pass `N*2`, it's rejected.

### Flashing binary onto microcontroller

To flash code onto MSP430 microcontroller you will need a MSP-FET programmer
from TI, either as a [standalone USB device](http://www.ti.com/tool/msp-fet)
or built-in into any MSP430 LaunchPad like [this
one](http://www.ti.com/tool/msp-exp430fr5994).

The following software must be installed for this to work:

* [mspdebug](https://dlbeer.co.nz/mspdebug/) (for Arch Linux, package
  [`mspdebug`](https://aur.archlinux.org/packages/mspdebug/) on AUR) 
* [TI Debug Stack for MSP430](http://www.ti.com/tool/mspds), aka. `tilib` in
  `mspdebug` (for Arch Linux, package [`mspds`](https://aur.archlinux.org/packages/mspds/) on AUR).

See earlier in this document for example for how to install packages from AUR
on Arch Linux, using `yaourt` helper or manually.

Maker provides a target `make bld/xxx/prog` (where `xxx` is the toolchain, e.g.
`gcc`) to flash the ELF binary.

#### Patch for mspdebug to not run program after flashing

Sometimes, esp. when performing experiments on intermittent power, it is
undesirable for the program to start running immediately after flashing, while
the device is still connected to a wired power supply (via the MSP-FET
programmer). Instead, you might want to simply disconnect the power after
having flashed the binary, and then power the device using the harvester,
so that the application only ever runs on harvested energy.

[This patch](mspdebug-no-run-after-flash.patch) to mspdebug prevents it from
issuing the run command after flashing. On Arch Linux, you can build a patched
package and install it, like so:

    wget https://aur.archlinux.org/cgit/aur.git/snapshot/mspdebug.tar.gz
    tar xf mspdebug.tar.gz
    cd mspdebug
    makepkg -so
    cd src/mspdebug-0.25
    wget https://github.com/CMUAbstract/releases/raw/master/mspdebug-no-run-after-flash.patch
    patch -p1 < mspdebug-no-run-after-flash.patch
    cd ../.. 
    makepkg -ei

ARM
---

To build code and flash the binary onto CC2650 microcontrollers (with BLE
radios), we have used TI Code Composer Studio (CCS).

TI CCS is packaged for Arch Linux in the
[ccstudio](https://aur.archlinux.org/packages/ccstudio) package on AUR which
also has a binary build available in [this
repository](http://arch.alexeicolin.com/arch/ti/), which you can add by adding
to `/etc/pacman.conf`:

    Server = http://arch.alexeicolin.com/arch/ti/$arch

Then, install with `pacman -Syu ccstudio`.

You will also need the following packages:

* [ti-cgt-arm](https://aur.archlinux.org/packages/ti-cgt-arm/) : TI GCC for ARM
* [ti-ble-sdk](https://aur.archlinux.org/packages/ti-ble-sdk/) : BLE stack

Ideally, in the future, Maker will get support for building using the ARM
toolchain.
