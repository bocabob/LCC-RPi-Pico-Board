# Raspberry Pi Pico for LCC

KiCad OpenLCB / LCC Raspberry Pi Pico motherboard for model railroad control.

Documentation, source, and gerbers for the board may be found in this repository:
<https://github.com/bocabob/LCC-RPi-Pico-Board>

## Overview

This board provides a flexible Raspberry Pi Pico (or Pico 2) platform for OpenLCB/LCC nodes. It includes:

- 3.3V and 5V switching power regulation
- Provision for onboard or plug-attached EEPROM or FRAM NVM modules
- Configurable to be powered from LCC, powered independently, or to inject power onto the LCC bus
- Tested with the OpenLCB Single Thread and OpenLcbCLib libraries

## Evolution

The LCC Pico development boards evolved from LocoNet designs dating back to 2021. The early turntable boards used Arduino Nanos and depended on turnout commands for indexing to hard-coded stepper positions. Fascia panel input was desired, so a keypad version was developed. This proved cumbersome, so a small touch screen was added with a Mega processor. The screen size needed to grow to nine inches for usability, so the Raspberry Pi Pico was selected. The Pico represents the "Goldilocks" processor of very good function, technical stability, and the easy-to-use Arduino IDE. Moving to the Pico enabled the LCC migration in 2024, when the Single Thread library began to support the RP2040 processor family. With OpenLcbCLib, full protocol support was enabled.

Earlier revisions also included a dedicated **Stepper** board with its own stepper motor driver circuitry built in. As of v3.0, that circuitry has moved to a separate breakout board that plugs into the standard Node board, so there is no longer a need for a Stepper-specific board design — one Node board now serves every node type.

## Family

There is a single board design: the **Node**. It exposes 16 GP pins through two 10P IDC connectors, plus an additional 5-pin analog header (I/O-3), for general-purpose I/O. Specialized functions — stepper motor control, displays, servos, etc. — are added via plug-in breakout boards rather than dedicated board variants.

While the board works with either the Pico or the Pico 2, the Pico 2 is recommended for its enhanced capabilities.

## Common Features

### Power

The board provides dual switching outputs of 3.3V and 5V, with input either from the LCC CAN bus or an external 8–15V source. It may be configured to inject power onto the CAN bus, or to isolate power on either or both of the CAN bus jacks. Auxiliary 3.3V and 5V outputs are available on the board.

### CAN Bus Control

Uses the discrete MCP2518 and MCP2562 CAN controller/transceiver hardware (easier to hand-solder than the integrated MCP251863 used on some earlier boards).

### Non-Volatile Memory (NVM)

I2C non-volatile memory should be used to reduce wear on the Pico flash. External NVM may be attached via the I2C header, the FRAM Breakout header, or soldered down to the IC5 footprint. When using the latter two, the I2C address jumpers may be used to select non-default addresses or implement write protection. Otherwise, those functions are handled by the module plugged into the I2C header.

Typical parts include modules using an AT25C256 chip or RC256V FRAM. Parts compatible with the following pinouts should work for soldering directly to the footprint:

- BR24G512F-3AGTE2 I2C BUS (2-Wire) 512K, or other sizes with equivalent pinouts
- MB85RC256VPF-G-BCERE1 256kbit FRAM with I2C

### I2C

I2C is used to address NVM. One or both of the available I2C channels on the Pico are exposed via headers for external devices. Care should be taken when attaching peripherals to the same channel as the NVM, to avoid interference. Some I2C devices require 5V, so a jumper selects between the two available voltages.

### Buttons

Since the Pico has no reset button, one is provided on the board. There is also a header so a remote reset button may be wired in. Provision is included for blue and gold buttons; on v3.0 these share pins with the I/O-2 and I/O-3 connectors (see pin table below), so they are unavailable when a breakout uses those specific pins.

### IDC Connections

GP pins and other connections are available on 10P IDC connectors (I/O-1, I/O-2), plus a 5-pin analog header (I/O-3). Pinouts are printed on the silkscreen and in the board user guide. On each 10P connector, pin 5 is Ground and pin 6 is a jumper-selectable voltage rail (3.3V or 5V) for the attached breakout.

## Board Variants

### LCC-Pico-Node v3.0

- MCP2518 and MCP2562 chips for CAN bus implementation of OpenLCB/LCC protocols
- Two 10-pin IDC connectors (I/O-1, I/O-2) exposing 16 GP pins for general-purpose I/O
- One 5-pin analog header (I/O-3) for additional GP/analog connections
- Two I2C ports
- Stepper, display, servo, or other specialized functions are added via plug-in breakout boards rather than being built into the Node board itself

### IDC Breakout Boards

- **Display 101 Adapter** — attaches to a BuyDisplay ER-TFTM101 10.1" touchscreen for SPI display control and I2C touch. Includes provision for alternate assignments of line 10 and a reset button.
- **Stepper Breakout** *(planned — design files not yet in this repository)* — carries a stepper motor driver and provides connections to the stepper motor, sensors, NeoPixel bus, and an I2C connection. Has a separate power connection for the external stepper motor supply (12–15V) and for the +5V used for Hall sensors and the NeoPixel bus; the IDC connector voltage should be set to 3.3V on the Node side when this breakout is attached.
- **Ribbon Breakout** — IDC breakout with optional LEDs for testing.
- **Servo Breakout** — IDC breakout with power and ground pins around each GPIO pin to facilitate connections to inputs, servos, NeoPixels, or other devices.

## Board Pin Assignments

| Pin | GP       | Alt 1     | Alt 2    | v3.0 Node                          |
|-----|----------|-----------|----------|------------------------------------|
| 1   | gp0      | SPI0 RX   | I2C0 SDA | ACAN_RX_PIN                        |
| 2   | gp1      | SPI0 CSN  | I2C0 SCL | ACAN_CS_PIN                        |
| 3   | GND      |           |          |                                     |
| 4   | gp2      | SPI0 SCK  | I2C1 SDA | ACAN_CSK_PIN                       |
| 5   | gp3      | SPI0 TX   | I2C1 SCL | ACAN_TX_PIN                        |
| 6   | gp4      | SPI0 RX   | I2C0 SDA | ACAN_INT_PIN                       |
| 7   | gp5      | SPI0 CSn  | I2C0 SCL | I/O-2: Pin 10 & Blue Button        |
| 8   | GND      |           |          |                                     |
| 9   | gp6      | SPI0 SCK  | I2C1 SDA | I2C1 SDA                           |
| 10  | gp7      | SPI0 TX   | I2C1 SCL | I2C1 SCL                           |
| 11  | gp8      | SPI1 RX   | I2C0 SDA | I/O-1: Pin 1                       |
| 12  | gp9      | SPI1 CSn  | I2C0 SCL | I/O-1: Pin 2                       |
| 13  | GND      |           |          |                                     |
| 14  | gp10     | SPI1 SCK  | I2C1 SDA | I/O-1: Pin 3                       |
| 15  | gp11     | SPI1 TX   | I2C1 SCL | I/O-1: Pin 4                       |
| 16  | gp12     | SPI1 RX   | I2C0 SDA | I/O-1: Pin 7                       |
| 17  | gp13     | SPI1 CSn  | I2C0 SCL | I/O-1: Pin 8                       |
| 18  | GND      |           |          |                                     |
| 19  | gp14     | SPI1 SCK  | I2C1 SDA | I/O-1: Pin 9                       |
| 20  | gp15     | SPI1 TX   | I2C1 SCL | I/O-1: Pin 10                      |
| 21  | gp16     | SPI0 RX   | I2C0 SDA | I/O-2: Pin 1                       |
| 22  | gp17     | SPI0 CSn  | I2C0 SCL | I/O-2: Pin 2                       |
| 23  | GND      |           |          |                                     |
| 24  | gp18     | SPI0 SCK  | I2C1 SDA | I/O-2: Pin 3                       |
| 25  | gp19     | SPI0 TX   | I2C1 SCL | I/O-2: Pin 4                       |
| 26  | gp20     |           | I2C0 SDA | I/O-2: Pin 7                       |
| 27  | gp21     |           | I2C0 SCL | I/O-2: Pin 8                       |
| 28  | GND      |           |          |                                     |
| 29  | gp22     |           |          | I/O-2: Pin 9                       |
| 30  | RUN      |           |          | Reset Button / header              |
| 31  | gp26     | ADC0      | I2C1 SDA | I/O-3: Pin 1                       |
| 32  | gp27     | ADC1      | I2C1 SCL | I/O-3: Pin 2                       |
| 33  | GND      | A GND     |          | I/O-3: Pin 3 (AGND)                |
| 34  | gp28     | ADC2      |          | I/O-3: Pin 5 & Gold Button         |
| 35  |          | ADC_VREF  |          | I/O-3: Pin 4 (VREF)                |
| 36  | 3v3_OUT  |           |          |                                     |
| 37  | 3V3_EN   |           |          |                                     |
| 38  | GND      |           |          |                                     |
| 39  | VSYS     |           |          |                                     |
| 40  | VBUS     |           |          |                                     |
|     | GND      |           |          | I/O-1: Pin 5                       |
|     | Vselect1 |           |          | I/O-1: Pin 6                       |
|     | GND      |           |          | I/O-2: Pin 5                       |
|     | Vselect2 |           |          | I/O-2: Pin 6                       |

Note: I/O-2 Pin 10 (gp5) and I/O-3 Pin 5 (gp28) double as the Blue and Gold buttons. A breakout that uses either of those pins makes the corresponding button unavailable.

## Software

### Example Node Applications

- <https://github.com/bocabob/LCC_RPiPico_Turntable>
- <https://github.com/bocabob/LCC_RPiPico_Roundhouse>
- <https://github.com/bocabob/LCC_RPiPico_PixelLights>
- <https://github.com/bocabob/LCC_RPiPico_Clock_Lights>

### Architectural Standard

Cross-project firmware conventions (board versioning, breakout pin assignments, configuration memory, naming) for all node applications above are documented in:
<https://github.com/bocabob/LCC_RPiPico_Common>

### Board Support

Use the RP2040 board library by Earle Philhower:
<https://arduino-pico.readthedocs.io/en/latest/install.html>

### LCC Support

- <https://github.com/openlcb/OpenLCB_Single_Thread>
- <https://github.com/JimKueneman/OpenLcbCLib>

### CAN Hardware

- ACAN2517 Arduino library

### External Storage

- I2C_EEPROM Arduino library by Rob Tillaart
- FRAM_I2C Arduino library by Rob Tillaart

### NeoPixel

- NeoPixelBus by Makuna (preferred)
- NeoPixelConnect by Alan Yorinks

### Other Preferred Libraries

#### Display

- `TFT_eSPI_RA8876.h` — display library (modified): <https://github.com/bocabob/TFT_eSPI_RA8876>
- `RA8876_RP2040.h` — display library (modified): <https://github.com/bocabob/RA8876_RP2040>
- `my_bb_captouch.h` — capacitive touch driver (modified): <https://github.com/bocabob/my_bb_captouch>

#### Servos

- `ServoEasing.hpp` — for slow servo action, including bounces
- `PCA9685_servo_driver.h`
- `PCA9685_servo.h`

#### Stepper Motor

- `AccelStepper.h` by Mike McCauley (modified)
