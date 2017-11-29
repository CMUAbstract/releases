Mementos: Long-Running Computation on RFID-Scale Devices
========================================================

[Mementos](https://spqr.eec.umich.edu/mementos) (B.Ransford, J.Sorber,
K.Fu, ASPLOS'11) is a system for supporting computation that
spans power failures on energy-harvesting devices. The system includes a
compiler pass that inserts conditional checkpoints into the program and a
runtime system that decides whether to record each checkpoint based on current
energy stored on the device.  Each checkpoint copies volatile state (registers
and stack) into non-volatile memory.  After each reboot, the runtime restores
the checkpoint by copying the data from non-volatile memory back into volatile
memory and registers.

Please refer to the linked webpage from the authors of Mementos for further
information. Our [fork of Mementos](https://github.com/CMUAbstract/mementos)
re-packages the source code so that applications built with the
[Maker](https://github.com/CMUAbstract/maker) dependency-build system, can
be built with Mementos, just as they can be built with [Alpaca](Alpaca.md),
[Chain](Chain.md), [DINO](DINO.md). Our fork adds recipes for Maker so that,
when it builds the application, it also builds and invokes the Mementos
compiler passes on the application bytecode, as well as builds and links the
Mementos runtime library. In Maker-built applications, the builds using
different systems/toolchains coexist alongside each other.
