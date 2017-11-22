EDBsat
======

Synopsis
--------

EDBsat (ASPLOS\'18,
[TPC\'17](http://sunsite.informatik.rwth-aachen.de/Publications/AIB/2017/2017-09.pdf))
is a battery-less solar-powered nano-satellite in the
[KickSat](https://www.kickstarter.com/projects/zacinaction/kicksat-your-personal-spacecraft-in-space)
form-factor of a single 35x35mm PCB board. The mission is to evaluate
energy-harvesting technology as a low-cost platform for scientific exploration
of low-earth orbit. Our board has two MCUs: a CC430 for the radio stack and
built-in [EDB](#edb)-based profiler and an MSP430FRxxxx with FRAM non-volatile
memory for the application written in [Chain](#chain). The application samples
temperature, accelerometer, and magnetometer, and sends the data to Earth via
the radio link. The profiler collects a histogram of charge on the capacitor at
the beginning of each task in the application.

The power system uses supercapacitors to accumulate and provide a burst of
energy that is sufficient to transmit a 2-byte packet to Earth, even when there
is zero incoming power from the solar powers during the transmission (e.g., due
to poor orientation). This allows the same board to be used in terrestrial
applications, including in cloudy weather, or near windows indoors.

The firmware for the radio+profiler MCU is in
[edb-sat-edb](https://github.com/CMUAbstract/edb-sat-edb), which includes the
[KickSat SpriteRadio stack](https://github.com/kicksat/arduino_sprite) re-packaged
for [Maker](https://github.com/CMUAbstract/maker). The application MCU runs the
[app-space-data-chain](https://github.com/CMUAbstract/app-space-data-chain)
application.

The ground station for receiving transmissions is built around an SDR dongle
and GNU radio software as described in the [KickSat
wiki](https://github.com/kicksat/groundstation/wiki). Our ground station RF hardware
is the [NooElec R820T2/RTL2838 DVB-T
Stick](http://www.nooelec.com/store/sdr/nesdr-hf-mini-2-plus-bundle.html),
[Advanced Receiver P432VDG](http://www.advancedreceiver.com/page5.html) amplifier,
and [Arrow Antenna 440-5](http://arrowantennas.com/arrowii/440-5ii.html). Our
[fork of the GNU radio decoder
software](https://github.com/CMUAbstract/groundstation) (packaged in
[PKGBUILD](https://github.com/CMUAbstract/sprite-groundstation-PKGBUILD) for
Arch Linux for easy installation) decodes packets in real-time on an [Odroid
XU4 ARM
board with fan](http://www.hardkernel.com/main/products/prdt_info.php?g_code=G143452239825)
(passive heatsink is not enough), which has Cortex 4xA7+4xA15 big.LITTLE, and
displays packets on an [Odroid Show2 LCD
display](http://www.hardkernel.com/main/products/prdt_info.php?g_code=G141743018597).
Our groundstation hardware also includes a battery and power conditioning for
portable operation (TODO: details, upstream).

The GNU radio decoder requires [FFTW](http://www.fftw.org) library
([fftw](https://archlinuxarm.org/packages/armv7h/fftw) package on Arch Linux for ARM)
to be rebuilt with ARM NEON SIMD support to make it fast enough for realtime.
On Arch Linux on the XU4 board (or other armv7h boards with Cortex-A\* cores
with NEON support), build from [this forked
PKGBUILD](https://github.com/CMUAbstract/fftw-PKGBUILD) branch
`armv7-a`; on other distros, use the flags and build commands from this
PKGBUILD manually. Before invoking the decoder, enable access to performance
counters for the FFTW library using a kernel module linked from [FFTW
docs](https://github.com/FFTW/fftw3/blob/master/README-perfcnt.md). When
invoking the decoder, set CPU affinity to only the
\"big\" A15 cores (avoiding the \"SMALL\" A7 cores) by prefixing the command with
`taskset 0xf0`.

The scripts in
[edb-sat-edb/decoder](https://github.com/CMUAbstract/edb-sat-edb/tree/master/decoder)
parse the received bytes, decoded by the GNU radio decoder, into EDBsat packets
and display the bytes and the packets on the LCD display.

[Request an EDBsat board from us](http://abstract.ece.cmu.edu) and/or see the
hardware design files (TODO: release).
