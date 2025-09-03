# DIY Bruce ESP32 Gamepad

**A custom, feature-rich Flipper Zero alternative with a gamepad layout**

Built with Bruce Firmware for the ESP32-S3 and an ILI9341 TFT Display.

---

## 🔍 Overview

This project guides you through building a powerful, handheld security and development tool inspired by the Flipper Zero. It runs a custom configuration of the popular **Bruce firmware** on an **ESP32-S3** microcontroller. The device features a game-controller-style layout, with a joystick for navigation and multiple buttons for input, centered around a 2.8" color TFT display. This document provides a comprehensive guide covering hardware selection, pin configuration, step-by-step assembly, and firmware installation.

## ⚡ Hardware Specifications

### Core Processor & Memory
- **Microcontroller**: ESP32-S3 WROOM1 N16R8 (+ OV5640 Cam)
- **CPU**: Dual-core Xtensa LX7 up to 240 MHz
- **Flash Memory**: 16 MB
- **PSRAM**: 8 MB
- **Storage**: MicroSD card slot

### Display & Input
- **Display**: 2.8" ILI9341 TFT LCD (320×240 pixels)
- **Navigation**: Dual-axis XY Joystick with push-button
- **Controls**: 4x Tactile push-buttons (Right-hand)
- **Controls (Optional)**: 2x Tactile push-buttons (Top-mounted)

### Wireless Communication
- **WiFi**: Built-in ESP32-S3 802.11 b/g/n
- **Bluetooth**: Built-in ESP32-S3 Bluetooth 5.0 LE
- **Sub-GHz Radio**: CC1101 transceiver (315/433/868/915 MHz)
- **2.4GHz Radio (Optional)**: nRF24L01+ transceiver
- **NFC/RFID**: PN532 controller (I²C interface)

### Infrared Capabilities
- **IR Transmitter**: 5mm IR LED
- **IR Receiver**: TSOP1738 IR sensor for signal capture

### Power System
- **Battery**: 2000 mAh Li-Po Battery
- **Charging**: TC4056 USB-C Charger Module
- **Voltage Regulation**: LM2596T 3.3V Regulator
- **Power Control**: 2-pin rocker switch

---

## 📌 Pin Configuration

This build utilizes a shared SPI bus for the Display, MicroSD card, CC1101, and nRF24 modules.

### Shared SPI Bus (VSPI)
| Function | ESP32-S3 Pin | Description                |
|----------|--------------|----------------------------|
| SCK      | GPIO 18      | SPI Clock                  |
| MOSI     | GPIO 23      | Master Out, Slave In       |
| MISO     | GPIO 19      | Master In, Slave Out       |

### ILI9341 TFT Display
| Function | ESP32-S3 Pin | Description                |
|----------|--------------|----------------------------|
| CS       | GPIO 12      | Chip Select                |
| DC       | GPIO 2       | Data/Command Select        |
| RST      | GPIO 4       | Reset                      |
| BL       | 3.3V         | Backlight Power            |

### MicroSD Card Module (Optional)
| Function | ESP32-S3 Pin | Description                |
|----------|--------------|----------------------------|
| CS       | GPIO 16      | Chip Select                |

### CC1101 Sub-GHz Radio
| Function | ESP32-S3 Pin | Description                |
|----------|--------------|----------------------------|
| CSN (CS) | GPIO 5       | Chip Select                |
| GDO0     | GPIO 13      | Interrupt/Status Pin       |

### nRF24L01+ 2.4GHz Radio (Optional)
| Function | ESP32-S3 Pin | Description                |
|----------|--------------|----------------------------|
| CSN (CS) | GPIO 17      | Chip Select                |
| CE       | GPIO 6       | Chip Enable                |
| IRQ      | GPIO 1       | Interrupt Pin              |

### I²C Bus (PN532 NFC)
| Function | ESP32-S3 Pin | Description                |
|----------|--------------|----------------------------|
| SDA      | GPIO 21      | I²C Data Line              |
| SCL      | GPIO 22      | I²C Clock Line             |

### Infrared Interface
| Function | ESP32-S3 Pin | Description                |
|----------|--------------|----------------------------|
| IR RX    | GPIO 7       | IR Receiver Data Out       |
| IR TX    | GPIO 8       | IR Transmitter Anode       |

### Navigation & Controls
| Function           | ESP32-S3 Pin | Description                |
|--------------------|--------------|----------------------------|
| Joystick X-Axis    | GPIO 36      | Analog Input               |
| Joystick Y-Axis    | GPIO 39      | Analog Input               |
| Joystick Button    | GPIO 15      | Digital Input (Active Low) |
| Right Button 1 (Up)| GPIO 11      | Digital Input (Active Low) |
| Right Button 2 (Down)| GPIO 14      | Digital Input (Active Low) |
| Right Button 3 (Back)| GPIO 9       | Digital Input (Active Low) |
| Right Button 4 (OK)  | GPIO 10      | Digital Input (Active Low) |
| Top-Left Button    | GPIO 26      | Digital Input (Active Low) |
| Top-Right Button   | GPIO 32      | Digital Input (Active Low) |

---

## 🛠️ Build & Flash Guide

### Prerequisites & Tools
- All components listed in the Hardware Specifications.
- **Tools**: Soldering iron, breadboard, jumper wires, wire cutters, multimeter.
- **Software**: VS Code with PlatformIO, Arduino IDE, or a modern web browser.

### Step-by-Step Assembly
This guide ensures a logical workflow, starting with the power system to prevent damage to sensitive components.

**1. Assemble the Power System**
*   **Connect Battery to Charger**: Solder the Li-Po battery leads to the `B+` and `B-` terminals on the TC4056 charger module.
*   **Wire the Power Switch**: Solder the `OUT+` from the TC4056 to one pin of your rocker switch. Solder a wire from the other pin of the switch to the `VIN` (input) of the LM2596T regulator.
*   **Connect Regulator Ground**: Solder the `OUT-` from the TC4056 directly to the `GND` (input) of the LM2596T.
*   **Set Output Voltage**: Before connecting the ESP32, connect the battery and turn the power switch on. Use a multimeter to measure the output voltage of the LM2596T. Carefully turn the potentiometer on the regulator until the output is a stable **3.3V**. This is a critical step.
*   **Power Off**: Turn the switch off before proceeding.

**2. Connect Core and Input Components**
*   **Mount ESP32-S3**: Place the ESP32-S3 on your breadboard or protoboard.
*   **Power the ESP32**: Connect the `3.3V` output from the LM2596T to the `3V3` pin on the ESP32, and the `GND` output to a `GND` pin on the ESP32.
*   **Connect the Display**:
    *   Connect the display's VCC and GND pins to the 3.3V and GND rails.
    *   Connect the shared SPI pins (`SCK`, `MOSI`, `MISO`) from the ESP32 to the corresponding pins on the ILI9341.
    *   Connect the display-specific control pins (`CS`, `DC`, `RST`) to their assigned GPIOs on the ESP32 as per the pinout table.
*   **Wire the Joystick and Buttons**:
    *   Connect the joystick's `VRx` and `VRy` pins to `GPIO 36` and `GPIO 39`.
    *   Connect the joystick's `SW` pin and all other tactile buttons to their assigned GPIOs.
    *   Connect the other leg of each button to `GND`. The firmware will use internal pull-up resistors.

**3. Wire Communication Modules**
*   **Connect Shared SPI Modules (MicroSD, CC1101, nRF24)**:
    *   Connect the `SCK`, `MOSI`, and `MISO` pins of each module in parallel to the shared SPI bus (GPIO 18, 23, 19).
    *   Connect the unique Chip Select (`CS` or `CSN`) pin of each module to its specific GPIO as defined in the pinout table (e.g., MicroSD CS to GPIO 16, CC1101 CSN to GPIO 5).
    *   Connect any additional control pins (`GDO0`, `CE`, `IRQ`) to their respective GPIOs.
    *   Power each module from the 3.3V and GND rails.
*   **Connect I²C Module (PN532)**:
    *   Connect the PN532's `SDA` and `SCL` pins to GPIO 21 and 22 on the ESP32.
    *   Power the module from the 3.3V and GND rails.

**4. Final Connections & Verification**
*   **Connect IR Components**:
    *   Connect the signal pin of the TSOP1738 receiver to `GPIO 7`. Connect its VCC and GND to the 3.3V rails.
    *   Connect the Anode (+) of the IR LED to `GPIO 8`. Connect the Cathode (-) through a 220-330Ω current-limiting resistor to `GND`.
*   **Final Check**: With the power switch OFF, use a multimeter in continuity mode to check for shorts between the `3.3V` and `GND` rails. If there are no shorts, you are ready to apply power.

### Firmware Installation
Choose one of the following methods to flash the Bruce firmware.

#### Method 1: Bruce Web Flasher (Recommended)
This is the simplest method and requires no software installation.
1.  **Enter Bootloader Mode**: Hold the **BOOT** button on the ESP32-S3, connect it to your PC via USB, then release the button.
2.  **Launch Flasher**: Open a Chrome/Edge browser and go to **[https://bruce.computer/flasher](https://bruce.computer/flasher)**.
3.  **Flash Firmware**:
    *   Select "Latest Release" and **"Custom Boards Launcher"**.
    *   Click **CONNECT** and select the serial port of your device.
    *   Click **INSTALL**. **Crucially, check the "Erase device" box** before confirming.
4.  **Reboot**: Once complete, unplug and replug the USB cable to start the device.

#### Method 2: VS Code with PlatformIO (Recommended for Customization)
Ideal for developers who want to modify the source code.
1.  **Setup VS Code**:
    *   Install Visual Studio Code.
    *   Install the **PlatformIO IDE** extension from the marketplace.
2.  **Download Source Code**:
    *   Clone the repository: `git clone https://github.com/sivabala21/Bruce.git`
    *   Open the cloned folder in VS Code (`File > Open Folder...`).
3.  **Configure and Build**:
    *   PlatformIO will automatically detect the `platformio.ini` file and may prompt you to install necessary libraries and platforms. Allow it to do so.
    *   The `platformio.ini` file contains different environments (e.g., `[env:lord-board]`). Ensure the pin definitions in the source code (`/src/` or `/include/`) match your pinout table.
4.  **Upload Firmware**:
    *   Put your ESP32-S3 into **Bootloader Mode** (Hold BOOT, connect USB, release BOOT).
    *   In VS Code, click the **PlatformIO icon** (alien head) in the activity bar.
    *   Under your project's environments, find the correct target and click **Upload**. Alternatively, use the command palette (`Ctrl+Shift+P`) and run `PlatformIO: Upload`.

#### Method 3: Arduino IDE
This method is for users who prefer compiling from source within the Arduino ecosystem.
1.  **Setup Arduino IDE**:
    *   Install the Arduino IDE.
    *   Add the ESP32 board manager URL in Preferences: `https://raw.githubusercontent.com/espressif/arduino-esp32/gh-pages/package_esp32_index.json`
    *   Install "esp32" from the Boards Manager.
2.  **Download Source Code**:
    ```bash
    git clone https://github.com/sivabala21/Bruce.git
    ```
3.  **Install Libraries**: Open the sketch in the Arduino IDE. Use the Library Manager to install **LovyanGFX** and any other missing libraries indicated by the compiler.
4.  **Verify Pinout**: Open the project's configuration files (e.g., in an `include/configs` folder) and double-check that the pin definitions match the tables in this document. Adjust if necessary.
5.  **Build and Upload**:
    *   Select Board: `Tools > Board > ESP32S3 Dev Module`.
    *   Configure Board Settings: Enable `OPI PSRAM`.
    *   **Enter Bootloader Mode** (see Method 1).
    *   Click **Upload**.

---

## 📚 Firmware Features

- **Bruce Firmware Base**: Leverages the powerful and feature-rich core of Bruce firmware.
- **Multi-Radio Control**: Simultaneously manage Sub-GHz, 2.4GHz, and NFC communications.
- **Gamepad UI**: Intuitive joystick and button-based navigation for a fluid user experience.
- **Infrared Control**: Learn, save, and emulate infrared remote signals.
- **USB HID**: Emulate a keyboard or mouse for BadUSB-style payloads.
- **File System**: Use the MicroSD card for storing captures, payloads, and themes.
- **Extensible**: The open-source nature allows for endless customization and new features.

---

## 🤝 Contributing

This is a community-driven project based on Bruce firmware. Contributions are highly encouraged.
- Improve hardware drivers and power optimization.
- Implement new features or fix bugs.
- Enhance documentation for clarity.

---

## 📄 License

This firmware is based on Bruce firmware and adheres to its licensing terms. The hardware guide and custom configurations are provided for educational and research purposes.

---

## ⚠️ Disclaimer

This device and its firmware are intended for educational, research, and authorized security testing purposes only. The user is solely responsible for ensuring compliance with all applicable local, state, and federal laws.

---

## 🙏 Acknowledgments

- **The Bruce Firmware Team** for creating the exceptional open-source firmware that powers this device.
- **The ESP32 & Open-Source Hardware Communities** for their invaluable documentation, libraries, and support.
