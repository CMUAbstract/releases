Alpaca: Intermittent Programs without Checkpoints
=================================================

## Synopsis

Alpaca (OOPSLA\'17) is a task-based programming model for writing reliable
software for intermittent, energy-harvesting computer systems. Alpaca programs
are written as a set of tasks that access shared data in non-volatile memory
and private data in task-local variables. The programmer uses macros defined in
`libalpaca` library to declare tasks and identify the shared variables. A
compiler pass detects which of the variables need to be protected such that the
tasks have a consistent view of the data despite reboots due to power failure.
The compiler inserts code to privatize protected data and commit it atomically
upon transition to the following task. The runtime library implements the
two-phase commit.

The compiler passes, the runtime library, and Maker recipes (see
[below](#building-applications-with-alpaca) are released in the [Alpaca
repository](https://github.com/CMUAbstract/alpaca). The documentation contains
a summary of the [programming interface to
Alpaca](https://github.com/CMUAbstract/alpaca#programming-interface) and
instructions for describes [how to build applications with
Alpaca](https://github.com/CMUAbstract/alpaca#building-applications-with-alpaca).
Also, complete example applications written with Alpaca are available in the
[list of benchmarks in the release
documentation](https://github.com/CMUAbstract/releases), and can serve as a
template for quickly creating a custom application.

## Snapshot

A snapshot of the Alpaca source code, benchmark applications, and result data
files with experimental results associated with the OOPSLA\'17 publication is
available in the
[alpaca-oopsla2017](https://github.com/CMUAbstract/alpaca-oopsla2017)
repository. The full development environment associated with this snapshot is
available in [this virtual
machine](https://drive.google.com/file/d/0B_5ymIVM2bU3N29hOWdodjhvTmM/view?usp=sharing).
