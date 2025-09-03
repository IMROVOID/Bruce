# My First DIY Security Tool: The Bruce ESP32 Gamepad

**A step-by-step guide to building your own Flipper Zero alternative with a simple gamepad design.**

This project uses the powerful and fun **Bruce Firmware** on an **ESP32-S3** brain, with a user-friendly ILI9341 color screen.

---

## 🔍 What Are We Building?

Welcome! This guide will walk you through building a handheld device for exploring the world of technology. Think of it as a pocket-sized multi-tool for electronics and security research. It's built with a game controller layout, making it easy and intuitive to use. We'll cover picking your parts, wiring everything together, and getting the software running.

## ⚡ The Parts List (Hardware Specifications)

Here's a breakdown of the components you'll need.

### The "Brain"
- **Microcontroller**: ESP32-S3 WROOM1 N16R8. This is the main chip that runs everything.
- **Flash Memory**: 16 MB (to store the firmware).
- **PSRAM**: 8 MB (extra memory for smooth operation).
- **Storage**: A MicroSD card slot (to save files, just like in a camera).

### Screen and Controls
- **Display**: 2.8" ILI9341 TFT LCD. A bright, colorful screen to see what's happening.
- **Navigation**: An XY Joystick, like on a game controller, for moving through menus.
- **Buttons**: A set of tactile push-buttons for selecting options.

### Wireless Tools
- **WiFi & Bluetooth**: Built right into the ESP32-S3.
- **Sub-GHz Radio**: A CC1101 module to interact with devices like garage door openers and remote controls.
- **2.4GHz Radio** (Optional): An nRF24L01+ module for other common wireless signals.
- **NFC/RFID**: A PN532 module to read things like key cards and payment tags.

### Infrared (Like a TV Remote)
- **IR Transmitter**: An IR LED to send signals.
- **IR Receiver**: A TSOP1738 sensor to capture and record signals.

### Power System
- **Battery**: A 2000 mAh Li-Po Battery to make it portable.
- **Charging**: A TC4056 USB-C board to safely charge the battery.
- **Voltage Regulation**: An LM2596T module. This is very important as it safely steps down the battery's voltage to the 3.3V that our components need.
- **Power Switch**: A simple rocker switch to turn it on and off.

---

## 📌 Wiring Guide (Pin Configuration)

Think of this as the recipe for connecting everything to the right place on the ESP32 brain. Each component needs to talk to the ESP32 through a specific "pin" (GPIO).

### The Main Data Highway (Shared SPI Bus)
Some parts can share the same data lines to save space. We connect them all to these three pins:
| Function | ESP32-S3 Pin |
|----------|--------------|
| SCK      | GPIO 18      |
| MOSI     | GPIO 23      |
| MISO     | GPIO 19      |

### Color Screen (ILI9341 TFT)
| Function | ESP32-S3 Pin |
|----------|--------------|
| CS       | GPIO 12      |
| DC       | GPIO 2       |
| RST      | GPIO 4       |
| BL       | 3.3V         |

### MicroSD Card
| Function | ESP32-S3 Pin |
|----------|--------------|
| CS       | GPIO 16      |

### Sub-GHz Radio (CC1101)
| Function | ESP32-S3 Pin |
|----------|--------------|
| CSN (CS) | GPIO 5       |
| GDO0     | GPIO 13      |

### 2.4GHz Radio (nRF24L01+)
| Function | ESP32-S3 Pin |
|----------|--------------|
| CSN (CS) | GPIO 17      |
| CE       | GPIO 6       |
| IRQ      | GPIO 1       |

### NFC Reader (PN532)
This uses a different, simpler connection called I²C.
| Function | ESP32-S3 Pin |
|----------|--------------|
| SDA      | GPIO 21      |
| SCL      | GPIO 22      |

### Infrared (IR Blaster)
| Function | ESP32-S3 Pin |
|----------|--------------|
| IR RX    | GPIO 7       |
| IR TX    | GPIO 8       |

### Joystick & Buttons
| Function           | ESP32-S3 Pin |
|--------------------|--------------|
| Joystick X-Axis    | GPIO 36      |
| Joystick Y-Axis    | GPIO 39      |
| Joystick Button    | GPIO 15      |
| Right Button 1 (Up)| GPIO 11      |
| Right Button 2 (Down)| GPIO 14      |
| Right Button 3 (Back)| GPIO 9       |
| Right Button 4 (OK)  | GPIO 10      |
| Top-Left Button    | GPIO 26      |
| Top-Right Button   | GPIO 32      |

---

## 🛠️ Let's Build It! (Assembly & Flashing)

### Tools You'll Need
- A soldering iron and some solder.
- A breadboard or protoboard for assembly.
- Jumper wires.
- Wire cutters/strippers.
- A multimeter (very important for testing!).

### Assembly Steps: One Step at a Time

We'll build the power system first. This is the safest way to avoid accidentally damaging the ESP32 brain.

**Step 1: Build a Safe and Tested Power Supply**
1.  **Battery to Charger**: Solder the red wire (`+`) of your battery to the `B+` pad on the TC4056 charger. Solder the black wire (`-`) to the `B-` pad.
2.  **Add the Power Switch**: Take the `OUT+` wire from the charger and solder it to one leg of your power switch. Solder another wire from the switch's other leg to the `IN+` pad of your LM2596T voltage regulator.
3.  **Connect Ground**: Solder the `OUT-` from the charger directly to the `IN-` pad on the regulator.
4.  **CRITICAL - Set the Voltage!**: Before you connect anything else, plug in the battery and flip the power switch ON. Use your multimeter to measure the voltage between the `OUT+` and `OUT-` pads of the LM2596T regulator. Use a small screwdriver to gently turn the tiny screw (potentiometer) on the blue box until your multimeter reads **3.3 Volts**. This ensures you send the correct, safe voltage to all your components.
5.  **Power Down**: Flip the switch OFF. Your power supply is now ready and safe!

**Step 2: Connect the Brain and Screen**
1.  **Power the ESP32**: Connect the `OUT+` (which is now 3.3V) from your regulator to the `3V3` pin of the ESP32. Connect `OUT-` to a `GND` pin on the ESP32.
2.  **Hook up the Screen**: Connect the screen's VCC pin to the 3.3V power and GND to ground. Then, wire up the SPI (`SCK`, `MOSI`, `MISO`) and control pins (`CS`, `DC`, `RST`) to the GPIO numbers listed in the pinout table.

**Step 3: Add the Controls**
1.  **Wire the Joystick**: Connect the joystick's X and Y pins to their GPIOs. Connect the switch (`SW`) pin and all your other buttons to their GPIOs.
2.  **Ground the Buttons**: Connect the other leg of every single button (including the joystick switch) to Ground (GND). Don't worry about other resistors; we'll use the ESP32's internal ones.

**Step 4: Wire the Wireless Modules**
1.  **SPI Modules (SD Card, CC1101, nRF24)**: Connect the `SCK`, `MOSI`, and `MISO` pins of all these modules to the same three shared ESP32 pins. Then, give each module its own unique `CS` connection to the GPIO listed in the table. This `CS` pin is how the ESP32 chooses which module it wants to talk to.
2.  **NFC Module (PN532)**: This one is easier. Just connect `SDA` to its GPIO and `SCL` to its GPIO.

**Step 5: The Final Touches**
1.  **IR Receiver**: The TSOP1738 has three legs. Connect its VCC to 3.3V, GND to ground, and the Signal pin to `GPIO 7`.
2.  **IR LED**: Connect the long leg (Anode, `+`) of the IR LED to `GPIO 8`. Connect the short leg (Cathode, `-`) to one end of a 220Ω or 330Ω resistor. Connect the other end of the resistor to `GND`. **Don't skip the resistor**, or the LED will burn out instantly!
3.  **Safety Check!**: Before you turn it on for the first time, use your multimeter's "continuity" mode (the one that beeps) to check that your `3.3V` and `GND` connections are not touching anywhere. If it beeps, you have a short circuit! Find and fix it before you proceed.

### Getting the Software (Firmware) Installed

#### Method 1: The Easy Way (Web Flasher)
This is best for beginners. You don't need to install any coding software.
1.  **Bootloader Mode**: Find the "BOOT" button on your ESP32. Hold it down. While holding it, plug the ESP32 into your computer with a USB cable. Then you can release the button.
2.  **Go Online**: Open the Chrome or Edge web browser and navigate to **[https://bruce.computer/flasher](https://bruce.computer/flasher)**.
3.  **Flash It**:
    *   Choose "Latest Release" and "Custom Boards Launcher".
    *   Click **CONNECT** and pick the port your ESP32 is on.
    *   Click **INSTALL**. **This is important:** check the "Erase device" box to ensure a clean installation.
4.  **Restart**: Unplug and replug the USB. Your device should now boot up!

#### Method 2: The Coder's Way (VS Code + PlatformIO)
If you want to tinker with the code, this is the method for you.
1.  **Get Set Up**: Install VS Code, then find and install the "PlatformIO IDE" extension from the Extensions tab.
2.  **Get the Code**: Download the code by cloning it: `git clone https://github.com/sivabala21/Bruce.git`. Open the folder in VS Code.
3.  **Build**: PlatformIO is smart and will ask to install what it needs. Let it. The main configuration is in the `platformio.ini` file.
4.  **Upload**: Put the ESP32 in **Bootloader Mode** as described in Method 1. In VS Code, click the little alien head icon for PlatformIO, find your project, and click "Upload".

#### Method 3: The Classic Way (Arduino IDE)
1.  **Setup Arduino**: Install the Arduino IDE and add the ESP32 boards to it via the Preferences menu.
2.  **Get the Code**: Download the code just like in the PlatformIO method.
3.  **Add Libraries**: Open the project. The IDE will probably complain about missing libraries. Use the Library Manager (`Sketch > Include Library > Manage Libraries...`) to find and install **LovyanGFX** and any others it needs.
4.  **Check Your Pins**: Make sure the pins defined in the code match your wiring from the tables above!
5.  **Upload**: Select "ESP32S3 Dev Module" as your board. Put the device in **Bootloader Mode** and click the "Upload" arrow.

---

## 📚 What Can It Do? (Firmware Features)

- **Explore Radio Signals**: Control Sub-GHz, 2.4GHz, and NFC devices.
- **Easy to Use**: A simple, intuitive interface you can navigate with the joystick.
- **Universal Remote**: Learn and copy signals from your TV remotes.
- **Computer Tricks**: Act like a keyboard or mouse to run commands on a PC (use responsibly!).
- **Save Everything**: Store your captures, payloads, and themes on the MicroSD card.
- **Make It Your Own**: It's all open-source, so you can change it however you like!

---

## ⚠️ A Quick Disclaimer

This is a powerful tool meant for learning and experimentation. Please be responsible and respect others' privacy and security. Only use it on your own devices and networks. Know and follow your local laws.

---

## 🙏 Thank You!

A huge thank you to the **Bruce Firmware Team** for creating such an amazing piece of software and to the entire **open-source community** for making projects like this possible. Happy building!
