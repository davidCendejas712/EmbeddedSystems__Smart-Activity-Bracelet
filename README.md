# Smart Activity Bracelet Project (PSyD 2023-24)

This project made in 2023–24 is a simulation of an activity-tracking wristband capable of monitoring physical activity. This device measures steps and heart rate while providing a user interface (e.g. display, buttons, alarms). The project was developed in a bare-metal embedded systems context: the PSyD course emphasizes low‑level C/ASM programming, direct hardware control, and real‑time operation. In particular, the firmware runs without any OS or high‑level libraries, using the on‑chip peripherals directly.

A demonstration video of the working prototype is provided [here](https://youtu.be/_2kJxMbubrM).

The .zip is on the main branch, the uncompressed files are on the master branch.

## Hardware & Platforms

Microcontroller: The design uses a Samsung S3C44B0X (ARM7TDMI) microcontroller on a dedicated development board (Embest S3CEV40). The ARM7 core and built-in peripherals provide timers, ADC, UART, etc.

Sensors: The bracelet tracks motion and pulse. In a typical build, a 3‑axis accelerometer detects steps, and a heart‑rate sensor connects to an ADC input. In our implementation we used a PC-based “sensors emulator” that sends simulated step/pulse data via UART into the MCU.

Display & UI: We used a 320×240 graphics LCD (and/or 7‑segment LEDs) to show menus and readings. Pushbuttons or touch-screen inputs let the user navigate. A buzzer or audio codec (UDA1341) is driven by the MCU for alarms. LEDs and segments indicate status. The provided BSP libraries included drivers for LCD, keypad, LEDs, 7‑segment, UART, RTC, timers, etc.

Communication: A UART (via USB/FTDI) connects the MCU to the PC/app for sensor data and debugging. (In a final product this could be replaced by Bluetooth LE or another wireless link.)

## Software & Development

Languages & Toolchain: All firmware is written in C (with assembly routines) using an ARM GCC/Eclipse toolchain.

Real-Time Operation: Timers and interrupts handle time-sensitive tasks. For example, a hardware timer periodically updates the step count and heart-rate calculation in the background, while the main loop updates the display. All peripheral I/O (buttons, sensors) is interrupt-driven to ensure responsiveness.

Data Handling: Steps and heartbeats are accumulated and stored in memory; an internal clock/RTC can timestamp readings. The firmware supports basic scheduling (via flags and timer ticks) to scan sensors continuously, independent of UI activity.

## Multithreading and Continuous Tracking

To keep tracking of steps and pulses at all times, the system uses concurrent execution: the sensor-reading routines run in parallel with the UI code. For instance, hardware interrupts (or a dedicated data thread in an app) update the step count and heart-rate every second, while the main program handles menu navigation. This way, data collection is independent of which screen or function is active. In practice, we set flags in interrupt handlers and use a simple scheduler so that sensor updates and alarms can occur in “background threads” while the user is elsewhere in the application.

## Technologies Summary

MCU & Board: ARM7 (Samsung S3C44B0X) development board (e.g. Olimex), 32‑bit RISC architecture.

Programming: C with inline ASM, no OS, Eclipse/GCC toolchain.

Peripherals: LCD display, LEDs/segments, buttons or touchscreen, UART, ADC, timers, RTC, I²C (for EEPROM), audio codec (UDA1341)

Sensors: Accelerometer (for steps), analog PPG heart-rate sensor (via ADC). In our demo, data comes from a PC-based UART sensor emulator.

Software Practices: Interrupt-driven I/O, state machines for menu logic, and optional multi-threading in the host app to allow continuous monitoring across UI screens.

## Authors
David Cendejas Rodríguez and Marcos Gómez Cortés

