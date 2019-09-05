Coati: Transactional Concurrency Control for Intermittent Systems
=================================================================

## Synopsis

Coati ([PLDI\'19](https://dl.acm.org/citation.cfm?id=3314583)) 
simplifies the process of adding asynchronous events to code that will run on an intermittent,
energy-harvesting computing system.
Coati provides support for event-driven concurrency via interrupts  in an intermittent software execution model.
Coati exposes a task-based interface for synchronous computations and an event interface
for asynchronous interrupts. Coati supports synchronizing
tasks and events using transactions, which allow for multitask atomic regions that extend across multiple power failures.
Coati further decomposes events into top and bottom halves. The top half runs immediately to perform time-critical operations
and queues a bottom half to run at a later time that can perform latency insensitive calculations.
We implemented the Coati runtime as a set of C-language extensions in [libcoati](https://github.com/CMUAbstract/coati).

## Test Directory

[Coati test ecosystem](https://github.com/CMUAbstract/coati_pldi19): contains a directory setup to compare interrupt driven
benchmarks written using [Alpaca](Alpaca.md), Coati and Buffi. Buffi is similar to Coati but uses substantial buffering to allow
interrupts to safely execute in their entirety immediately when they are triggered.

[Coati test artifact](https://github.com/CMUAbstract/coati_pldi19/tree/artifact): contains a slightly modified version of the artifact
submitted [PLDI 2019's artifact evaluuation](https://pldi19.sigplan.org/track/pldi-2019-PLDI-Research-Artifacts). 
In the original artifact, the user could remotely connect to an
energy-harvesting [Capybara](Capybara.md) board and run the experiments comparing Alpaca, Coati, and Buffi. The connection is no longer active,
but the accompanying [documentation](https://github.com/CMUAbstract/coati_pldi19/blob/artifact/README_detailed.pdf) 
details the experimental setup.

