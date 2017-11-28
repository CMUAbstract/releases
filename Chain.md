Chain: Task-based Programming Model for Intermittent Software
=============================================================

Synopsis
--------

Chain ([OOPSLA\'16](http://dl.acm.org/citation.cfm?id=2983995)) is a programming
model for the intermittent execution model, that arises on intermittently-powered
energy-harvesting devices. The programming model is implemented using a set of
extensions to the C language with a runtime library,
[libchain](https://github.com/CMUAbstract/libchain). To write a program using
Chain, the application source must include the header and link against the
library.

In the Chain programming model, applications are expressed as a graph of
statically-defined tasks with explicit control flow statements that transfer
control from one task to the next. Tasks store *local* data in variables
allocated on the (volatile) stack and *shared* data in persisted channels
statically allocated in non-volatile memory. The programming interface
provided by `libchain` includes macros for declaring tasks and channels,
and for accessing channels from task code. The programming interface
is specified in the `libchain`
[documentation](https://github.com/CMUAbstract/libchain).

Chain implementation supports MSP430 microcontrollers with FRAM non-volatile
memory. [Applications written in Chain](README.md#benchmark-applications) have
been tested on the [WISP5](http://wisp5.wikispaces.com/WISP+Home) RF-powered
device, on the [Capybara](Capybara.md) board, the [EDBsat](EDBsat.md) board, and on TI
MSP430FRxxxx Launchpads.

[Blinker](https://github.com/CMUAbstract/app-blinker-chain) is an example
application written in Chain, which can be used a template to create other
applications. The application is built using
[Maker](https://github.com/CMUAbstract/maker) which automatically builds the
`libchain` dependency when the application is built.
