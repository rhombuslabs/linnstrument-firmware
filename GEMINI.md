# GEMINI.md

## Project Overview

This project contains the firmware for the LinnStrument, a musical instrument controller. It is written in C++ for the Arduino Due platform.

The firmware is responsible for all aspects of the LinnStrument's functionality, including:

*   **Touch Sensing:** Reading and interpreting data from the touch surface.
*   **LED Control:** Driving the LEDs to provide visual feedback to the user.
*   **MIDI Communication:** Sending and receiving MIDI messages over USB and DIN MIDI ports.
*   **Sequencer and Arpeggiator:** Implementing the built-in sequencer and arpeggiator features.
*   **Configuration:** Managing a wide range of user-configurable settings, which are stored in non-volatile flash memory.

The project is structured in a modular way, with different `.ino` files responsible for different aspects of the firmware. The main file is `linnstrument-firmware.ino`, which contains the `setup()` and `loop()` functions.

## Building and Running

### Arduino IDE

The `README.md` file specifies that the project should be built with **Arduino IDE v1.8.1** and **SAM boards v1.6.11**.

1.  Open the `linnstrument-firmware.ino` file in the Arduino IDE.
2.  Select the "Arduino Due (Programming Port)" board from the "Tools" > "Board" menu.
3.  Connect the LinnStrument to your computer via USB.
4.  Select the correct serial port from the "Tools" > "Port" menu.
5.  Click the "Upload" button to build and upload the firmware.

### Arduino CLI

The project can also be built from the command line using the `arduino-cli` tool.

**1. Core Installation**
```
arduino-cli core install arduino:sam
```

**2. Compile**
```
arduino-cli compile --fqbn arduino:sam:arduino_due_x linnstrument-firmware.ino
```

**3. Upload**
```
arduino-cli upload --fqbn arduino:sam:arduino_due_x --port <your-port> linnstrument-firmware.ino
```
*Replace `<your-port>` with the actual port of your LinnStrument.*

## Development Conventions

*   **Modular Structure:** The code is organized into multiple `.ino` files, each with a specific responsibility (e.g., `ls_arpeggiator.ino`, `ls_sequencer.ino`). This is a common convention in larger Arduino projects.
*   **Header Files:** Header files (`.h`) are used to define constants, data structures, and function prototypes that are shared between multiple `.ino` files.
*   **Configuration Structs:** A set of nested `struct`s is used to manage the large number of configuration settings. This provides a clear and organized way to access and manipulate the settings.
*   **Fixed-Point Math:** The firmware uses fixed-point arithmetic for some calculations to improve performance.
*   **Direct Hardware Access:** The code directly accesses hardware registers and uses low-level libraries like `SPI.h` and `efc.h` for performance-critical operations.
*   **Comments:** The code is well-commented, with detailed explanations of complex logic and hardware interactions.
