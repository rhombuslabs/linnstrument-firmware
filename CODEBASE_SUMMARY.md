# LinnStrument Firmware Codebase Summary

The LinnStrument firmware is a professional-grade, deeply complex Arduino (C++) project. Its purpose is to be the complete operating system for the LinnStrument musical controller.

## Core Architecture:
The architecture is event-driven and modular, centered around a main loop in `linnstrument-firmware.ino`.
1.  **Main Loop:** The `loop()` function continuously scans the touch-sensitive surface cell by cell.
2.  **Sensor Reading (`ls_sensor.ino`):** For each cell, it performs low-level SPI communication to read raw X (pitch), Y (timbre), and Z (pressure) values. This process is highly optimized with per-cell calibration data (`Z_BIAS`) and dynamic settling delays to account for hardware variances.
3.  **Touch Logic (`linnstrument-firmware.ino`):** The raw sensor data is processed to detect new touches, touch releases, or movement on existing touches. This logic is encapsulated in `handleNewTouch()`, `handleTouchRelease()`, and `handleXYZupdate()`.
4.  **MIDI Output (`ls_midi.ino`):** Based on the touch events and the extensive user configuration, the firmware generates MIDI messages. It uses a non-blocking, queued system (`midiOutQueue`, `handlePendingMidi`) with sophisticated rate-limiting (decimation) and filtering to avoid flooding the MIDI bus. It fully supports MPE and can be controlled remotely via a comprehensive NRPN implementation.
5.  **Settings Management (`ls_settings.ino`):** All configuration is stored in a master `Configuration` struct. This file handles initializing, loading, and saving this configuration to the Due's flash memory using a robust double-buffering scheme to prevent corruption. It also contains a vast set of handlers that form a complete UI controller for modifying settings via the instrument's surface.
6.  **Display (`ls_displayModes.ino`, inferred):** The state of the system and settings menus is rendered on the built-in LEDs. The display is updated by calls to `updateDisplay()` after the model (the `Configuration` struct) is changed.

## Key Components:
- **Language:** C++ (Arduino framework).
- **Main File:** `linnstrument-firmware.ino` (main loop, core data structures).
- **MIDI:** `ls_midi.ino` (all MIDI I/O, queuing, filtering, NRPN control).
- **Sensor:** `ls_sensor.ino` (low-level hardware communication, calibration).
- **Settings:** `ls_settings.ino` (persistence, UI logic for configuration).
- **Other Modules:** Functionality is further broken down into modules for the arpeggiator (`ls_arpeggiator.ino`), sequencer (`ls_sequencer.ino`), LEDs (`ls_leds.ino`), etc., which are all implicitly included by the Arduino build process.

In essence, the project is a real-time embedded system that reads high-resolution sensor data, interprets it according to a deeply configurable ruleset, and translates it into expressive musical data via MIDI.

## Relevant Locations:

### `C:\Users\xboxl\git\rhombuslabs\linnstrument-firmware\linnstrument-firmware.ino`
- **Reasoning:** This is the main file of the project. It contains the Arduino `setup()` and `loop()` functions, which are the entry points of the firmware. It defines the core data structures like `Configuration` and `TouchInfo` that are used throughout the application. The main loop drives the sensor scanning and touch handling logic.
- **Key Symbols:** `setup()`, `loop()`, `Configuration`, `TouchInfo`, `handleNewTouch()`, `handleXYZupdate()`, `handleTouchRelease()`

### `C:\Users\xboxl\git\rhombuslabs\linnstrument-firmware\ls_midi.ino`
- **Reasoning:** This file implements the entire MIDI subsystem. `handleMidiInput` processes all incoming MIDI messages, including clock and extensive NRPN for remote control. The `midiSend...` functions, along with `queueMidiMessage` and `handlePendingMidi`, form a robust, non-blocking, and rate-limited output system. This is the core of the instrument's communication with the outside world.
- **Key Symbols:** `handleMidiInput()`, `handlePendingMidi()`, `queueMidiMessage()`, `midiSendNoteOn()`, `midiSendPitchBend()`, `receivedNrpn()`

### `C:\Users\xboxl\git\rhombuslabs\linnstrument-firmware\ls_sensor.ino`
- **Reasoning:** This file contains the low-level code for interacting with the sensor hardware. It manages the SPI communication with the analog switches and the ADC. It includes crucial hardware-specific logic like per-cell calibration tables (`Z_BIAS`) and dynamically adjusted settling delays to ensure accurate and consistent sensor readings.
- **Key Symbols:** `readX()`, `readY()`, `readZ()`, `selectSensorCell()`, `spiAnalogRead()`, `Z_BIAS`

### `C:\Users\xboxl\git\rhombuslabs\linnstrument-firmware\ls_settings.ino`
- **Reasoning:** This file manages the persistence and modification of all user settings. It handles loading settings from flash on boot and saving them back. It contains a large set of 'handler' functions that act as a controller, responding to user touches on the various settings menus to modify the state of the `Configuration` data structure. It uses a safe, double-buffered writing scheme to prevent data corruption.
- **Key Symbols:** `initializeStorage()`, `writeSettingsToFlash()`, `loadSettings()`, `handleControlButtonNewTouch()`, `handlePerSplitSettingNewTouch()`