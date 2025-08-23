# Complete Documentation for a Custom Bruce ESP32 Device

This guide walks you through building a powerful, handheld device using an ESP32-S3 WROOM1 N16R8 + OV5640 Cam, a 2.8" TFT LCD (ILI9341) display, and modules like CC1101, TSOP1738, and PN532. It’s designed to work with the Bruce firmware and features a game-controller-style layout with a joystick on the left, four action buttons on the right, and two optional shoulder buttons for intuitive navigation. The assembly section is super detailed, simple, and step-by-step, covering all components—even the optional ones—so anyone can follow along!

---

## Component Checklist

### Core Components

*   **Microcontroller**: ESP32-S3 WROOM1 N16R8 + OV5640 Cam (or a similar ESP32-S3 board)
    *   *Features: Dual-core Xtensa LX7 CPU up to 240 MHz, Wi-Fi 802.11 b/g/n, Bluetooth 5.0 LE, 16 MB Flash, 8 MB PSRAM, integrated 5MP OV5640 camera with 2592x1944 resolution.*
*   **Display**: 2.8" TFT LCD (ILI9341)
    *   *Specs: 320×240 pixels, SPI interface, 3.3V~5V, RGB 65K colors, white LED backlight, no touch sensor, with SD card slot*
*   **PCB Board**: A breadboard or custom PCB for assembly.
*   **Case/Enclosure**: To house the final project.

### Navigation Controls

*   **Joystick Module (Dual-axis XY)**: For primary navigation.
*   **Tactile Push Buttons** (6 units): Four for the right-hand action buttons and two for optional top-mounted shoulder buttons.

### Modules

*   **RF Module**: CC1101 RF Module (315/433/868/915 MHz)
*   **RFID/NFC Module**: PN532 NFC/RFID V3 (13.56 MHz)
*   **IR Components**
    *   TSOP1738 IR Receiver
    *   IR LED (5mm) (2 units)
*   **MicroSD Card Adapter Module** (Optional)
*   **NRF24 Module** (Optional)

### Power System

*   **Power Supply**: 2000 mAh Li-Po Battery
*   **Charger Module**: TC4056 Charger Module (USB-C, 1A)
*   **3.3V Regulator**: LM2596T (TO-220-5)
*   **On/Off Switch**: 2-pin Rocker Switch

### Wiring & Passive Components

*   **Jumper Wires**
    *   Male-to-Male (M→M) 10cm (40-pack)
    *   Male-to-Female (M→F) 10cm (40-pack)
    *   Female-to-Female (F→F) 10cm (40-pack)
*   **Pin Headers**
    *   Male 1x40 Long (2 units)
    *   Female 1x40 Right-Angle (2 units)
*   **Breadboard** (GL Brand, mini, 170 holes)
*   **Capacitors**: 10 µF, 16V, electrolytic (2 units)
*   **Resistors**
    *   330 Ω (0.25W, 5%, 50-pack)
    *   10 kΩ (0.25W, 5%, 50-pack)
*   **Transistors**: 2N2222, TO-92 (5 units)
*   **MicroSD Card**: 16GB

---

## Step-by-Step Assembly Guide

This section is your complete, super-detailed, and easy-to-follow guide to assembling the device. It covers every component—including optional ones—so you won’t miss a thing. Let’s build it together!

### Tools You’ll Need

Before starting, gather these tools:

*   Breadboard (mini, 170 holes)
*   Jumper Wires (M→M, M→F, F→F)
*   Soldering Iron (if you need to attach headers to modules)
*   Wire Cutters (to trim wires if they’re too long)
*   Multimeter (to check connections and avoid mistakes)
*   Screwdriver (to secure parts in the case later)

### Step 1: Set Up Your Workspace

1.  **Clear a Space**: Find a flat, clean table to work on.
2.  **Place the Breadboard**: Put your mini breadboard in the center. It’s your base for everything!
3.  **Gather Components**: Lay out all your parts (*CC1101*, *TSOP1738*, *jumper wires*, etc.) and tools so they’re easy to reach.
4.  **Get Ready**: Have the *ESP32-S3 WROOM1 N16R8 + OV5640 Cam*, *display*, and other parts handy.

### Step 2: Mount the ESP32-S3 WROOM1 N16R8 + OV5640 Cam

1.  **Get the ESP32-S3**: Take the *ESP32-S3 WROOM1 N16R8 + OV5640 Cam* out of the box.
2.  **Place It on the Breadboard**: Push its pins gently into the breadboard. Line it up so half the pins are on one side of the breadboard’s middle groove and half on the other. It should sit flat and firm.
3.  **Check the Pins**: Make sure no pins are bent and they’re all plugged in.
4.  **Temporary Power**: For now, plug the *ESP32-S3 WROOM1 N16R8 + OV5640 Cam* into your computer with a USB cable to power it. We’ll add the battery later.

### Step 3: Connect the Display (2.8" TFT LCD ILI9341)

The display shows the device’s interface. Let’s wire it up carefully.

1.  **Get the Display**: Find its pins: VCC, GND, SCK, SDA (or MOSI), CS, DC, RST, BL (backlight). Note: This display includes an SD card slot for expansion.
2.  **Power It Up**:
    *   Take a jumper wire (*M→F* works great).
    *   Plug one end into VCC on the display and the other into 3.3V on the *ESP32-S3* (look for a pin labeled *3V3* or *VIN*).
    *   Use another wire to connect GND on the display to GND on the *ESP32-S3*.
3.  **Hook Up the SPI Wires**:
    *   SCK (clock) → *ESP32-S3* GPIO 18 (use a wire to connect them).
    *   SDA (data, also called MOSI) → *ESP32-S3* GPIO 23.
4.  **Control Wires**:
    *   CS (chip select) → *ESP32-S3* GPIO 12 (adjusted to avoid conflicts with other components).
    *   DC (data/command) → *ESP32-S3* GPIO 2.
    *   RST (reset) → *ESP32-S3* GPIO 4.
5.  **Light the Screen**:
    *   Connect BL (backlight) to *ESP32-S3* 3.3V. If it’s too bright or you’re worried about power, add a 330 Ω resistor between *BL* and *3.3V*.
6.  **Double-Check**: Make sure all wires are secure and not loose. If using the display's built-in SD card slot instead of the separate module, wire its CS to a free pin (e.g., GPIO 16) and share SPI lines (SCK 18, MISO 19, MOSI 23).

### Step 4: Add the CC1101 RF Module

This module lets your device send and receive radio signals.

1.  **Find the Pins**: On your *CC1101*, look for CSN, SCK, MISO, MOSI, GDO0, VCC, GND.
2.  **Power and Ground**:
    *   Wire VCC to *ESP32-S3* 3.3V.
    *   Wire GND to *ESP32-S3* GND.
3.  **SPI Connections**:
    *   CSN → *ESP32-S3* GPIO 5 (this is its own special pin).
    *   SCK → *ESP32-S3* GPIO 18 (same as the display—it’s okay to share!).
    *   MISO → *ESP32-S3* GPIO 19.
    *   MOSI → *ESP32-S3* GPIO 23 (shared with the display).
4.  **Extra Option**:
    *   Connect GDO0 to *ESP32-S3* GPIO 13 if you want to detect signals (optional for now).
5.  **Check It**: Ensure no wires are touching where they shouldn’t.

### Step 5: Add the NRF24 Module (Optional)

This module provides 2.4GHz wireless communication.

1.  **Find the Pins**: On the NRF24, look for CE, CSN, SCK, MOSI, MISO, IRQ, VCC, GND.
2.  **Power and Ground**:
    *   Wire VCC to *ESP32-S3* 3.3V.
    *   Wire GND to *ESP32-S3* GND.
3.  **SPI Connections**:
    *   CSN → *ESP32-S3* GPIO 17 (this is its own special pin).
    *   CE → *ESP32-S3* GPIO 6.
    *   SCK → *ESP32-S3* GPIO 18 (shared with others).
    *   MISO → *ESP32-S3* GPIO 19 (shared).
    *   MOSI → *ESP32-S3* GPIO 23 (shared).
4.  **Extra Option**:
    *   Connect IRQ to *ESP32-S3* GPIO 1 if needed (optional).
5.  **Check It**: Ensure no wires are touching where they shouldn’t.

### Step 6: Wire the TSOP1738 IR Receiver

This catches infrared signals (like from a remote).

1.  **Locate Pins**: Find OUT, VCC, GND on the *TSOP1738* (it’s small, so look closely!).
2.  **Power It**:
    *   Connect VCC to *ESP32-S3* 3.3V.
    *   Connect GND to *ESP32-S3* GND.
3.  **Signal Wire**:
    *   Connect OUT to *ESP32-S3* GPIO 7.
4.  **Test Fit**: Make sure it’s snug on the breadboard.

### Step 7: Add the IR LED(s)

These send infrared signals.

1.  **Simple Setup**:
    *   Take one *IR LED*.
    *   Connect the longer leg (anode) to *ESP32-S3* GPIO 8.
    *   Connect the shorter leg (cathode) to a 330 Ω resistor, then connect the other end of the resistor to *ESP32-S3* GND.
2.  **Boosted Setup (Optional)**: If you want more range:
    *   Grab a 2N2222 transistor.
    *   Connect *ESP32-S3* GPIO 8 to the transistor’s base through a 1 kΩ resistor (use a *10 kΩ* and adjust if needed).
    *   Connect the transistor’s collector to 3.3V through the *IR LED* and a 100 Ω resistor (tweak with your *330 Ω* if needed).
    *   Connect the transistor’s emitter to GND.
3.  **Tip**: The simple setup works fine for most uses—try it first!

### Step 8: Connect the PN532 NFC/RFID Module

This reads NFC tags and cards.

1.  **Pin Check**: Find SDA, SCL, VCC, GND on the *PN532*.
2.  **Power and Ground**:
    *   Wire VCC to *ESP32-S3* 3.3V.
    *   Wire GND to *ESP32-S3* GND.
3.  **I²C Wires**:
    *   SDA → *ESP32-S3* GPIO 21.
    *   SCL → *ESP32-S3* GPIO 22.
4.  **Secure It**: Push it into the breadboard so it stays put.

### Step 9: Hook Up the Joystick Module

This is your main directional control.

1.  **Pin Check**: Find X-axis, Y-axis, Button (often labeled SW), VCC, and GND.
2.  **Power and Ground**:
    *   VCC → *ESP32-S3* 3.3V.
    *   GND → *ESP32-S3* GND.
3.  **Wiring**:
    *   X-axis → *ESP32-S3* GPIO 36 (Analog pin).
    *   Y-axis → *ESP32-S3* GPIO 39 (Analog pin).
    *   Button/SW → *ESP32-S3* GPIO 15.
4.  **Add Pull-up**: For the Button/SW pin (*GPIO 15*), connect a 10 kΩ resistor to 3.3V. This keeps it stable until pressed.
5.  **Move It**: Tilt it around to ensure it works smoothly.

### Step 10: Wire the Control Buttons

These are your right-hand action buttons. We will wire four of them.

1.  **Place Buttons**: Place four tactile push buttons on your breadboard.
2.  **Wire It Up**:
    *   Connect one side of all four buttons to *ESP32-S3* GND.
    *   Connect the other side of each button to a unique GPIO pin:
        *   Button 1 (e.g., "Up") → *ESP32-S3* GPIO 11.
        *   Button 2 (e.g., "Down") → *ESP32-S3* GPIO 14.
        *   Button 3 (e.g., "Left/Back") → *ESP32-S3* GPIO 9.
        *   Button 4 (e.g., "Right/OK") → *ESP32-S3* GPIO 10.
3.  **Add Pull-ups**:
    *   For each button pin (*GPIO 11, 14, 9, 10*), connect a 10 kΩ resistor from the GPIO pin to 3.3V. This keeps them “high” until you press a button, which pulls the pin to GND.
4.  **Test Press**: Push each button to make sure it feels right.

### Step 11: Add Top Buttons (Optional)

These act like shoulder buttons on a game controller.

1.  **Wire It Up**:
    *   Connect one side of two more tactile buttons to *ESP32-S3* GND.
    *   Connect the other side of each button to a unique GPIO pin:
        *   Top-Left Button → *ESP32-S3* GPIO 26.
        *   Top-Right Button → *ESP32-S3* GPIO 32.
2.  **Add Pull-ups**:
    *   Just like before, connect a 10 kΩ resistor from each pin (*GPIO 26, 32*) to 3.3V.

### Step 12: Add the MicroSD Module (Optional)

This lets you save logs or themes. Note: If using the ILI9341 display's built-in SD slot, you can skip this.

1.  **Pin Check**: Find CS, SCK, MISO, MOSI, VCC, GND on the module.
2.  **Power It**:
    *   Connect VCC to *ESP32-S3* 3.3V.
    *   Connect GND to *ESP32-S3* GND.
3.  **SPI Wires**:
    *   CS → *ESP32-S3* GPIO 16 (pick a free pin if 16 is used).
    *   SCK → *ESP32-S3* GPIO 18 (shared with others).
    *   MISO → *ESP32-S3* GPIO 19 (shared).
    *   MOSI → *ESP32-S3* GPIO 23 (shared).
4.  **Insert Card**: Pop in a *MicroSD card* to test later.

### Step 13: Set Up the Power System

This powers everything with a battery and switch.

1.  **Battery to Charger**:
    *   Connect the *2000 mAh Li-Po battery* + to *TC4056* B+ and – to *TC4056* B–.
2.  **Add the Switch**:
    *   Wire *TC4056* OUT+ to one side of the 2-pin rocker switch.
    *   Wire the other side of the switch to the LM2596T regulator input.
3.  **Regulator Setup**:
    *   Connect the regulator’s output to *ESP32-S3* 3.3V.
    *   Connect the regulator’s ground to *ESP32-S3* GND.
4.  **Finish Ground**:
    *   Wire *TC4056* OUT– to *ESP32-S3* GND.
5.  **Smooth It Out**:
    *   Add a 10 µF capacitor between *ESP32-S3* *3.3V* and *GND*.
6.  **Test Flip**: Flip the switch to see if it powers on (once everything’s connected).

### Step 14: Check Everything

1.  **Look Over Wires**: Use your multimeter to make sure nothing’s touching where it shouldn’t.
2.  **Power Up**: Plug in the USB or flip the switch (once the battery’s ready). The display should light up with the *Bruce* logo after flashing.
3.  **Test Each Part**:
    *   **Display**: See if the screen shows the menu.
    *   **Joystick & Buttons**: Use the joystick to navigate menus. Use the right-hand buttons for selecting and going back.
    *   **IR Stuff**: Point a remote at the *TSOP1738*, test the *LED* with the firmware.
    *   **RF Module**: Check *CC1101* in the firmware.
    *   **NFC**: Tap a tag to the *PN532*.
    *   **Optional Parts**: Test the *MicroSD* and *top buttons* if connected.

### Step 15: Put It in a Case

1.  **Get the Case**: Get a case and cut holes for the *display*, *joystick*, *buttons*, *USB port*, *IR LED*, and *power switch*.
2.  **Fit Everything**: Place the breadboard, battery, and modules inside. Use screws or sticky pads to hold them.
3.  **Tidy Up**: Tuck wires neatly so the case closes.
4.  **Close It**: Snap or screw the case shut—you’re done!

---

## Controlling the Device

*   **Display**: Shows the interface sideways (*landscape mode*).
*   **Joystick**: Used for up/down/left/right navigation within menus.
*   **Joystick Button**: Typically used for selecting or entering a sub-menu.
*   **Right-Hand Buttons**:
    *   Can be configured in the firmware.
    *   A common layout is one button for **OK/Select** and another for **Back/Cancel**. The other two can be for custom actions.
*   **Top Buttons (Optional)**: Can be assigned to special functions within the Bruce firmware.
*   **IR & RF**: Use the menus to learn and send signals.
*   **NFC**: Tap tags to the PN532 module to read or copy them.

---

## Programming the Device

Flashing the Bruce firmware can be done in several ways. The first method is the easiest, while the other two use the Arduino IDE for more advanced customization.

### Method 1: The Easy Way (Official Web Flasher)

This is the highly recommended method for most users as it requires no software installation. [1]

1.  **Get Ready to Flash**:
    *   Ensure any necessary USB-to-serial drivers are installed for your ESP32-S3 board.
    *   Put the device in **Bootloader Mode**: Disconnect the USB cable, press and **hold** the "BOOT" button on the ESP32-S3, then reconnect the USB cable while still holding it. Release the button after a second.
2.  **Use the Flasher**:
    *   Open **https://bruce.computer/flasher** in a compatible browser (like Chrome or Edge).
    *   Select "Latest Release" and "Custom Boards Launcher."
    *   Click **CONNECT**, choose the correct serial port for your device, and click **INSTALL**.
    *   **Important**: Check the "Erase device" box for a clean install, then click "INSTALL" again. [2]
3.  **Restart**: Once done, disconnect and reconnect the USB cable. The Bruce firmware should boot up.

### Programming with the Arduino IDE: Initial Setup

Methods 2 and 3 require the Arduino IDE. If you don't have it set up for ESP32, follow these steps first:

1.  **Install Arduino IDE**: Download and install the latest version of the Arduino IDE (v2.0 or newer is recommended).
2.  **Add ESP32 Boards**:
    *   Go to `File > Preferences`.
    *   In the "Additional boards manager URLs" field, paste this URL: `https://raw.githubusercontent.com/espressif/arduino-esp32/gh-pages/package_esp32_index.json`
    *   Go to `Tools > Board > Boards Manager...`, search for "esp32", and install the package by Espressif Systems.

### Method 2: From Source with Arduino IDE (Official GitHub)

This method lets you compile the official source code but requires manual configuration for your custom hardware.

*   **Source**: [https://github.com/pr3y/Bruce](https://github.com/pr3y/Bruce)

1.  **Download the Code**: Go to the GitHub link above, click `Code > Download ZIP`, and extract the folder. Open the `Bruce-main` folder and you should see `Bruce.ino`.
2.  **Install Libraries**: The project depends on several external libraries. You must install them using the Arduino Library Manager (`Sketch > Include Library > Manage Libraries...`). Search for and install the following:
    *   **LovyanGFX** (for the display)
    *   **Adafruit GFX Library**
    *   **Adafruit BusIO**
    *   **Adafruit PN532** (for NFC)
    *   **ArduinoJson**
    *   You may need others depending on the features you use. The compiler will tell you if anything is missing.
3.  **Configure for Your Hardware**: This is the most critical step. You need to tell the code which GPIO pins you used.
    *   In the Arduino sketch folder, navigate to `include > configs`.
    *   Create a copy of an existing ESP32-S3 config file (e.g., `LILYGO_T_DECK.h`) and rename it `my_custom_board.h`.
    *   Open `my_custom_board.h` and carefully edit the `#define` statements for each pin (e.g., `PIN_DISPLAY_CS`, `PIN_JOYSTICK_X`, etc.) to match your wiring from the assembly guide. This step must be precise.
    *   Open `Bruce.ino` and add `#define BRUCE_CUSTOM_BOARD` at the top. Then, go to `include/configs/KnownBoards.h` and add a new section that includes your custom header when `BRUCE_CUSTOM_BOARD` is defined.
4.  **Select Board and Upload**:
    *   In the Arduino IDE, go to `Tools > Board` and select `ESP32S3 Dev Module`.
    *   Under `Tools`, make sure PSRAM is set to `OPI PSRAM`.
    *   Put your device into **Bootloader Mode** (see Method 1).
    *   Select the correct COM port under `Tools > Port`.
    *   Click the **Upload** button.

### Method 3: Pre-configured Source with Arduino IDE (Community Fork)

This method is easier than Method 2 because the code is already modified for the ILI9341 display. You may only need to adjust the control pins.

*   **Source**: [https://github.com/sivabala21/Bruce](https://github.com/sivabala21/Bruce)

1.  **Download and Prepare**: Download the code as a ZIP from the link above and extract it. Open `Bruce.ino` in the Arduino IDE.
2.  **Install Libraries**: Follow Step 2 from Method 2 to install the necessary libraries like **LovyanGFX** and others via the Library Manager.
3.  **Check Pin Configuration**:
    *   This fork is pre-configured for an ILI9341 display, so you shouldn't need to change the display pins.
    *   However, you **must** check the pin definitions for your controls (joystick, buttons) and other modules (CC1101, PN532, etc.). Find the relevant configuration file (likely in the `include/configs` folder) and verify that the GPIO numbers match your wiring. Adjust as needed.
4.  **Select Board and Upload**:
    *   Follow Step 4 from Method 2: select `ESP32S3 Dev Module`, enable `OPI PSRAM`, put the board in **Bootloader Mode**, select the COM port, and click **Upload**.

---

## Final Notes

*   **Display Setup**: The firmware should be configured for landscape mode. When using the Arduino IDE, the **LovyanGFX** library needs to be correctly configured for the ILI9341 driver within the project's source code.
*   **MicroSD**: The ILI9341 display often has a built-in SD card slot. You can use this as an alternative to the separate MicroSD module by wiring its CS pin.
*   **IR LED**: The simple setup is sufficient for most uses unless you require extended range.

Have fun building your device!
