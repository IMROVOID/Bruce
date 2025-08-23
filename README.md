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
- **Software**: Arduino IDE or the Bruce Web Flasher.

### Step-by-Step Assembly
The assembly process involves connecting each module to the ESP32-S3 according to the pinout tables above.

1.  **Mount Core Components**: Place the ESP32-S3 on a breadboard or custom PCB.
2.  **Connect Display**: Wire the ILI9341 display, connecting the power (VCC, GND), shared SPI (SCK, MOSI), and control (CS, DC, RST, BL) pins.
3.  **Connect Navigation**: Wire the joystick (X, Y, SW) and the right-hand/top buttons to their respective GPIO pins. Remember to use 10kΩ pull-up resistors for all buttons, connecting the GPIO pin to 3.3V.
4.  **Connect Radios**: Wire the CC1101 and optional nRF24 modules to the shared SPI bus, ensuring each has its unique CS pin connected.
5.  **Connect NFC**: Wire the PN532 module using the I²C pins (SDA, SCL).
6.  **Connect IR Components**: Wire the TSOP1738 receiver and the IR LED. Use a 330Ω current-limiting resistor for the IR LED.
7.  **Connect Power System**: Carefully assemble the power circuit. Connect the Li-Po battery to the TC4056 charger's B+/B- terminals. Wire the charger's OUT+/- through the power switch to the LM2596T regulator, which then supplies a stable 3.3V to the ESP32-S3 and all modules.
8.  **Final Check**: Use a multimeter to check for short circuits between power and ground before applying power for the first time.

### Firmware Installation
Choose one of the following methods to flash the Bruce firmware.

#### Method 1: Bruce Web Flasher (Recommended)
This is the simplest method and requires no software installation.
1.  **Enter Bootloader Mode**: Hold the **BOOT** button on the ESP32-S3, connect it to your PC via USB, then release the button.
2.  **Launch Flasher**: Open a Chrome/Edge browser and go to **[https://bruce.computer/flasher](https://bruce.computer/flasher)**.
3.  **Flash Firmware**:
    - Select "Latest Release" and **"Custom Boards Launcher"**.
    - Click **CONNECT** and select the serial port of your device.
    - Click **INSTALL**. **Crucially, check the "Erase device" box** before confirming.
4.  **Reboot**: Once complete, unplug and replug the USB cable to start the device.

#### Method 2: Arduino IDE
This method is for users who prefer compiling from source. We recommend the community fork pre-configured for the ILI9341 display.
1.  **Setup Arduino IDE**:
    - Install the Arduino IDE.
    - Add the ESP32 board manager URL in Preferences: `https://raw.githubusercontent.com/espressif/arduino-esp32/gh-pages/package_esp32_index.json`
    - Install "esp32" from the Boards Manager.
2.  **Download Source Code**:
    ```bash
    git clone https://github.com/sivabala21/Bruce.git
    ```
3.  **Install Libraries**: Open the sketch in the Arduino IDE. Use the Library Manager to install **LovyanGFX** and any other missing libraries indicated by the compiler.
4.  **Verify Pinout**: Open the project's configuration files (e.g., in an `include/configs` folder) and double-check that the pin definitions match the tables in this document. Adjust if necessary.
5.  **Build and Upload**:
    - Select Board: `Tools > Board > ESP32S3 Dev Module`.
    - Configure Board Settings: Enable `OPI PSRAM`.
    - **Enter Bootloader Mode** (see Method 1).
    - Click **Upload**.

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
