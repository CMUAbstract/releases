EDB: Energy-interference Free Debugger
======================================

Synopsis
--------

EDB ([IEEE
Micro](https://www.computer.org/csdl/mags/mi/2017/03/mmi2017030116.html),
[ASPLOS\'16](http://dl.acm.org/citation.cfm?id=2872409)) is a USB debugger for
intermittently-powered energy-harvesting devices that does not interfere with
the energy supply of the target device. With EDB, application software can be
debugged while it runs intermittently on harvested energy. Main debugging
primitives are concurrent tracing of energy, execution, and I/O activity,
setting breakpoint based on code and energy, measuring energy consumption of
code spans using watchpoints that record energy, and energy-neutral `printf`
statements by automatic energy compensation. 

EDB requires an electrical connection to the capacitor on the target
energy-harvesting device and (optionally) connection to RX and TX lines for
decoding incoming RFID messages and backscattered bits.  EDB was tested with
the [WISP5](http://wisp5.wikispaces.com/WISP+Home) RF-harvesting device, and
includes a socket that matches the WISP header (connections to the capacitor
and RF RX/TX lines must be made separately).

EDB software relies on the [libedb](https://github.com/CMUAbstract/libedb)
runtime library to be linked into the application. See
[edb-rat](https://github.com/CMUAbstract/edb-rat) for an example application
with the library linked in using our
[Maker](https://github.com/CMUAbstract/maker) dependency build system. The
README of the `edb-rat` app has a tutorial for the debugging features of EDB.
This application can be copied and modified as a template to quickly create an
application with immediate support for EDB.

The programmer\'s interface to EDB is
[edb-console](https://github.com/CMUAbstract/edb-console), a GDB-like console
that runs on a workstation computer and communicates to EDB over USB.

The firware that runs on the EDB board is
[edb-server](https://github.com/CMUAbstract/edb-server). Most EDB functionality
is factored into [libedbserver](https://github.com/CMUAbstract/libedbserver),
for easy inclusion of tracing/profiling/debugging functionality in-situ, onto
a microcontroller (on a separate voltage domain from the main application MCU)
and that might be already shared by another workload (e.g., see EDBsat).

[Request an EDB board from us](http://abstract.ece.cmu.edu) and/or see the
hardware design files (TODO: release).

Board bring-up
--------------

Plug the 2-pin jumper (M50-1900005, for Abstract Lab: in stock in EDB parts
box) into the 3-pin selector header on the side of the board, to select the 3v
setting.

Connect an MSP-FET device (either the standalone device or on any TI MSP430
Launchpad) to the P1 header (4-pin) on the side of the EDB board. You need will
need an adapter to connect to the 4-pin 0.050\'\' header:

* for standalone, from 14-pin JTAG female; we use the [MSP-FET
  Splitter/Adapter](https://github.com/CMUAbstract//msp-fet-splitter)
* for launchpad: from 0.1\'\' male headers; we use female/female jumper wires
  ([Adafruit 266](https://www.adafruit.com/product/266)) and the 4-pin variant
of the [0.1 to 0.050 adapter mini-board](https://github.com/CMUAbstract/adapter-50-100mil).

Fetch, build, and flash the software onto the board:

    git clone --recursive https://github.com/CMUAbstract/edb-server
    cd edb-server
    make bld/gcc/all
    make bld/gcc/prog

See the tutorial in the [edb-rat](https://github.com/CMUAbstract/edb-rat)
application to flash the edb-rat application onto the
[WISP5](https://wisp5.wikispaces.com/WISP+Home) to test EDB functionality.

Users
-----

We\'re happy to have shared EDB with the following research groups:

* [Embedded Software Group, TU Delft](http://www.es.ewi.tudelft.nl/)
* [NEJ Group, Department of ECE, University of Toronto](http://www.eecg.toronto.edu/~enright/)
* [Persist Research Lab, Clemson University](http://persist.cs.clemson.edu/)
* [Lab11, University of Michigan/UC Berkeley](https://lab11.eecs.umich.edu/)

