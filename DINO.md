DINO: Simpler, Safer Programming and Execution Model for Intermittent Systems
=============================================================================

[DINO](https://github.com/blucia0a/dino) (B.Lucia and B.Ransford, PLDI '15) is a
task framework for intermittent programs that guarantees consistency between
volatile and non-volatile state by versioning non-volatile state in
checkpoints of volatile states. The programmer inserts task boundaries into
the code, and a compiler pass identifies the non-volatile state that needs
to be versioned at each boundary and adds the versioning code to that
boundary. At runtime, when a task boundary is crossed, volatile state and
a version of some non-volatile state are copied to non-volatile memory.
On reboot, the volatile state is initialized from the saved copy, and
the non-volatile state is reverted to a consistent version.

Please refer to the linked webpage and publication from the authors of DINO for
further information. Our [fork of DINO](https://github.com/CMUAbstract/dino)
re-packages the source code so that applications built with the
[Maker](https://github.com/CMUAbstract/maker) dependency-build system, can
be built with DINO, just as they can be built with [Alpaca](Alpaca.md),
[Chain](Chain.md), [Mementos](Mementos.md). Our fork adds recipes for Maker so that,
when it builds the application, it also builds and invokes the DINO
compiler passes on the application bytecode, as well as builds and links the
DINO runtime library. In Maker-built applications, the builds using
different systems/toolchains coexist alongside each other.
