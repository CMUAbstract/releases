Capybara: Reconfigurable Energy-storage Architecture
====================================================

Synopsis
--------

Capybara (ASPLOS\'18) is a hardware design with a software interface for
creating energy-storage for energy-harvesting devices that provides bursts of
energy matched to the application energy demands.

The hardware consists of a circuit for a variable-capacity energy storage.  The
storage is implemented by connecting multiple capacitor banks with non-volatile
switches that connect and disconnect each bank at runtime.  The hardware design
is implemented in the Capybara board, which demonstrates the reconfigurable energy
storage hardware within a general-purpose solar-powered energy-harvesting device
with several sensors (magnetometer, accelerometer with gyroscope, gesture, gas)
and a BLE radio. The design files (schematics, board layout, BOM) are available
in the [capybara](https://github.com/CMUAbstract/capybara) repository.
Assembled boards are available [on request](https://abstract.ece.cmu.edu/).

The software interface, provided by the
[libcapybara](http://github.com/CMUAbstract/libcapybara) library, targets
applications written using the task-based programming model, and has been
tested with [Chain](Chain.md).  The interface gives the application an ability
to associate energy modes to tasks. An energy mode describes the energy demand
of the task.  The energy mode declarations are made using a set of macros and
function calls from `libcapybara`. The runtime automatically reconfigures the
hardware energy storage bank to a capacity that matches the energy demand. The
library also allows asynchronous tasks (that may be invoked based on an
external event) to request a burst of energy to be available by the time the
task runs. The runtime pre-charges a capacitor bank.  The interface reference
is available in the [libcapybara
documentation](https://github.com/CMUAbstract/libcapybara). To use the
interface, the application must include and link against `libcapybara`, which
is packaged as a dependency for the
[Maker](https://github.com/CMUAbstract/maker) build system.

The following example applications, written in Chain and built with Maker,
demonstrate Capybara:

* [Gesture Recognition](https://github.com/CMUAbstract/app-gesture):
recognizes proximity and gestures using the APDS sensor
* [TempAlarm](https://github.com/CMUAbstract/app-tempalarm):
monitor temperature and broadcasts a BLE packet if temperature exceeds a
predefined range
