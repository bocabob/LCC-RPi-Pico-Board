# Raspberry Pi Pico for LCC

KiCad OpenLCB / LCC Raspberry Pi Pico motherboards for model railroad control.

Documentation, source, and gerbers for the boards may be found in this repository:
<https://github.com/bocabob/LCC-RPi-Pico-Board>

## Overview

These boards provide a flexible Raspberry Pi Pico (or Pico 2) platform for OpenLCB/LCC nodes. They include:

- 3.3V and 5V switching power regulation
- Provision for onboard or plug-attached EEPROM or FRAM NVM modules
- Configurable to be powered from LCC, powered independently, or to inject power onto the LCC bus
- Tested with the OpenLCB Single Thread and OpenLcbCLib libraries

## Evolution

The LCC Pico development boards evolved from LocoNet designs dating back to 2021. The early turntable boards used Arduino Nanos and depended on turnout commands for indexing to hard-coded stepper positions. Fascia panel input was desired, so a keypad version was developed. This proved cumbersome, so a small touch screen was added with a Mega processor. The screen size needed to grow to nine inches for usability, so the Raspberry Pi Pico was selected. The Pico represents the "Goldilocks" processor of very good function, technical stability, and the easy-to-use Arduino IDE. Moving to the Pico enabled the LCC migration in 2024, when the Single Thread library began to support the RP2040 processor family. With OpenLcbCLib, full protocol support was enabled.

## Families

There are two basic designs: the **Node** and the **Stepper**.

- **Node** boards expose 16 GP pins through two 10P IDC connectors and are intended for general-purpose I/O.
- **Stepper** boards include a stepper motor driver and dedicate supporting GP pins for turntable / display control.

While the boards work with either the Pico or the Pico 2, the Pico 2 is recommended for its enhanced capabilities.

## Common Features

### Power

The boards provide dual switching outputs of 3.3V and 5V, with input either from the LCC CAN bus or an external 8–15V source. The boards may be configured to inject power onto the CAN bus, or to isolate power on either or both of the CAN bus jacks. Auxiliary 3.3V and 5V outputs are available on the board.

### CAN Bus Control

An early design uses the integrated MCP251863 CAN controller and transceiver, but later designs settled on the discrete MCP2518 and MCP2562 hardware. Both work fine; the discrete chips are simply easier to hand solder.

### Non-Volatile Memory (NVM)

I2C non-volatile memory should be used to reduce wear on the Pico flash. External NVM may be attached via the I2C header, the FRAM Breakout header, or soldered down to the IC5 footprint. When using the latter two, the I2C address jumpers may be used to select non-default addresses or implement write protection. Otherwise, those functions are handled by the module plugged into the I2C header.

Typical parts include modules using an AT25C256 chip or RC256V FRAM. Parts compatible with the following pinouts should work for soldering directly to the footprint:

- BR24G512F-3AGTE2 I2C BUS (2-Wire) 512K, or other sizes with equivalent pinouts
- MB85RC256VPF-G-BCERE1 256kbit FRAM with I2C

### I2C

I2C is used to address NVM. One or both of the available I2C channels on the Pico are exposed via headers for external devices. Care should be taken when attaching peripherals to the same channel as the NVM, to avoid interference. Some I2C devices require 5V, so a jumper selects between the two available voltages.

### Buttons

Since the Pico has no reset button, one is provided on the board. There is also a header so a remote reset button may be wired in. Provision is included for blue and gold buttons; later designs add LEDs for these two buttons.

### IDC Connections

GP pins and other connections are available on 10P IDC connectors. Pinouts vary by board and are printed on the silkscreen and in the corresponding user guide. Power is provided on pins 5 and 6 and is typically jumper-selectable, except for the Turntable connector on the Stepper board.

The 2.8 Node board uses the higher GPIO pins on the second IDC connector, exposing the analog pins for use as node peripherals.

## Board Variants

### LCC-Pico-Node v2.6

- MCP2518 and MCP2562 chips for CAN bus implementation of OpenLCB/LCC protocols
- Ports for four NeoPixel / WS28xx LED strings
- Port for 10-pin IDC connection of GPIO / power for peripheral devices
- Two I2C ports

### LCC-Pico-Node v2.7

Drives two 10P IDC connectors with a total of 16 GPIO pins. Intended to drive servos and lights, or to take inputs from detectors or buttons.

### LCC-Pico-Node v2.8

Same essential functionality as the 2.7, but exposes the analog GPIO pins as an alternate implementation, making them usable for node peripherals.

### LCC-Pico-Stepper v2.4 (A & B)

- MCP2518 and MCP2562 chips (A) or MCP251863 chip (B) for CAN bus implementation of OpenLCB/LCC protocols
- Port for parallel connection to a touch display panel
- A4988 stepper motor driver port
- Ports for 10-pin IDC connection of GPIO / power / stepper peripheral devices
- One I2C port
- Intended to control a turntable and a parallel-connected display

### LCC-Pico-Stepper v2.7

Provision for a stepper motor driver. Intended to control a turntable and an SPI-connected display.

### IDC Breakout Boards

- **Display 101 Adapter** — attaches to a BuyDisplay ER-TFTM101 10.1" touchscreen for SDI display control and I2C touch. Includes provision for alternate assignments of line 10 and a reset button.
- **Ribbon Breakout** — IDC breakout with optional LEDs for testing.
- **Servo Breakout** — IDC breakout with power and ground pins around each GPIO pin to facilitate connections to inputs, servos, NeoPixels, or other devices.

## Board Pin Assignments

| Pin | GP    | Alt 1     | Alt 2    | 2.5 Node            | 2.6 Node            | 2.7 Node                | 2.8 Node                  | 2.4 Stepper          | 2.7 Stepper                  |
|-----|-------|-----------|----------|---------------------|---------------------|-------------------------|---------------------------|----------------------|------------------------------|
| 1   | gp0   | SPI0 RX   | I2C0 SDA | header pin          | header pin          | I/O-1: Pin 1            | ACAN_RX_PIN               | Bridge sensor        | TT-Pin3 — Bridge sensor      |
| 2   | gp1   | SPI0 CSN  | I2C0 SCL | header pin          | header pin          | I/O-1: Pin 2            | ACAN_CS_PIN               | Home sensor          | TT-Pin2 — Home sensor        |
| 3   | GND   |           |          |                     |                     |                         |                           |                      |                              |
| 4   | gp2   | SPI0 SCK  | I2C1 SDA | NeoPixel 1          | NeoPixel 1          | I/O-1: Pin 3            | ACAN_CSK_PIN              | Stepper Direction    | Stepper Direction            |
| 5   | gp3   | SPI0 TX   | I2C1 SCL | NeoPixel 2          | NeoPixel 2          | I/O-1: Pin 4            | ACAN_TX_PIN               | Touch Interrupt      | TT-Pin4 — NeoPixel data line |
| 6   | gp4   | SPI0 RX   | I2C0 SDA | I2C0 SDA            | I2C0 SDA            | I/O-1: Pin 7 / I2C0 SDA | ACAN_INT_PIN              | Touch SDA            | header pin                   |
| 7   | gp5   | SPI0 CSn  | I2C0 SCL | I2C0 SCL            | I2C0 SCL            | I/O-1: Pin 8 / I2C0 SCL | header pin                | Touch SCL            | header pin                   |
| 8   | GND   |           |          |                     |                     |                         |                           |                      |                              |
| 9   | gp6   | SPI0 SCK  | I2C1 SDA | NeoPixel 3          | NeoPixel 3          | I/O-1: Pin 9            | I2C1 SDA                  | Display Data 0       | Stepper Enable               |
| 10  | gp7   | SPI0 TX   | I2C1 SCL | NeoPixel 4          | NeoPixel 4          | I/O-1: Pin 10           | I2C1 SCL                  | Display Data 1       | Stepper Step                 |
| 11  | gp8   | SPI1 RX   | I2C0 SDA | I/O-1: Pin 10       | I/O Pin 10          | I/O-2: Pin 1            | I/O-1: Pin 1              | Display Data 2       | I/O-2: Pin 1 — SPI1 SDI      |
| 12  | gp9   | SPI1 CSn  | I2C0 SCL | I/O-1: Pin 9        | I/O Pin 9           | I/O-2: Pin 2            | I/O-1: Pin 2              | Display Data 3       | I/O-2: Pin 2 — SPI1 CS       |
| 13  | GND   |           |          |                     |                     |                         |                           |                      |                              |
| 14  | gp10  | SPI1 SCK  | I2C1 SDA | I/O-1: Pin 8        | I/O Pin 8           | I/O-2: Pin 3            | I/O-1: Pin 3              | Display Data 4       | I/O-2: Pin 3 — SPI1 CLK      |
| 15  | gp11  | SPI1 TX   | I2C1 SCL | I/O-1: Pin 7        | I/O Pin 7           | I/O-2: Pin 4            | I/O-1: Pin 4              | Display Data 5       | I/O-2: Pin 4 — SPI1 SDO      |
| 16  | gp12  | SPI1 RX   | I2C0 SDA | I/O-1: Pin 4        | I/O Pin 4           | I/O-2: Pin 7            | I/O-1: Pin 7              | Display Data 6       | I/O-2: Pin 7 — Touch I2C0 SDA |
| 17  | gp13  | SPI1 CSn  | I2C0 SCL | I/O-1: Pin 3        | I/O Pin 3           | I/O-2: Pin 8            | I/O-1: Pin 8              | Display Data 7       | I/O-2: Pin 8 — Touch I2C0 SCL |
| 18  | GND   |           |          |                     |                     |                         |                           |                      |                              |
| 19  | gp14  | SPI1 SCK  | I2C1 SDA | I/O-1: Pin 2        | I/O Pin 2           | I/O-2: Pin 9            | I/O-1: Pin 9              | Stepper Enable       | I/O-2: Pin 9 — Touch INT     |
| 20  | gp15  | SPI1 TX   | I2C1 SCL | I/O-1: Pin 1        | I/O Pin 1           | I/O-2: Pin 10           | I/O-1: Pin 10             | Stepper Step         | I/O-2: Pin 10 — open         |
| 21  | gp16  | SPI0 RX   | I2C0 SDA | ACAN_RX_PIN         | ACAN_RX_PIN         | ACAN_RX_PIN             | I2C0 SDA                  | ACAN_RX_PIN          | ACAN_RX_PIN                  |
| 22  | gp17  | SPI0 CSn  | I2C0 SCL | ACAN_CS_PIN         | ACAN_CS_PIN         | ACAN_CS_PIN             | I2C0 SCL                  | ACAN_CS_PIN          | ACAN_CS_PIN                  |
| 23  | GND   |           |          |                     |                     |                         |                           |                      |                              |
| 24  | gp18  | SPI0 SCK  | I2C1 SDA | ACAN_CSK_PIN        | ACAN_CSK_PIN        | ACAN_CSK_PIN            | I/O-2: Pin 1              | ACAN_CSK_PIN         | ACAN_CSK_PIN                 |
| 25  | gp19  | SPI0 TX   | I2C1 SCL | ACAN_TX_PIN         | ACAN_TX_PIN         | ACAN_TX_PIN             | I/O-2: Pin 2              | ACAN_TX_PIN          | ACAN_TX_PIN                  |
| 26  | gp20  |           | I2C0 SDA | ACAN_INT_PIN        | ACAN_INT_PIN        | ACAN_INT_PIN            | I/O-2: Pin 3              | ACAN_INT_PIN         | ACAN_INT_PIN                 |
| 27  | gp21  |           | I2C0 SCL | Blue Button         | Blue Button         | Blue Button             | I/O-2: Pin 4 & Blue Button | NeoPixel data line  | Blue Button                  |
| 28  | GND   |           |          |                     |                     |                         |                           |                      |                              |
| 29  | gp22  |           |          | Gold Button         | Gold Button         | Gold Button             | I/O-2: Pin 7 & Gold Button | Display WR          | Gold Button                  |
| 30  | RUN   |           |          | Reset Button / hdr  | Reset Button / hdr  | Reset Button / hdr      | Reset Button / hdr        | Reset Button         | Reset Button / hdr           |
| 31  | gp26  | ADC0      | I2C1 SDA | I2C1 SDA            | I2C1 SDA            | I2C1 SDA                | I/O-2: Pin 8              | I2C SDA / Blue Button | I2C1 SDA                    |
| 32  | gp27  | ADC1      | I2C1 SCL | I2C1 SCL            | I2C1 SCL            | I2C1 SCL                | I/O-2: Pin 9              | I2C SCL / Gold Button | I2C1 SCL                    |
| 33  | GND   | A GND     |          | header pin          | header pin          | header pin              | I/O-2: Pin 7 alternative  |                      | header pin                   |
| 34  | gp28  | ADC2      |          | header pin          | header pin          | header pin              | I/O-2: Pin 10             | Display D/C          | header pin                   |
| 35  |       | ADC_VREF  |          | header pin          | header pin          | header pin              | I/O-2: Pin 6 alternative  |                      |                              |
| 36  | 3v3_OUT |         |          |                     |                     |                         |                           |                      |                              |
| 37  | 3V3_EN |          |          |                     |                     |                         |                           |                      |                              |
| 38  | GND   |           |          |                     |                     |                         |                           |                      |                              |
| 39  | VSYS  |           |          |                     |                     |                         |                           |                      |                              |
| 40  | VBUS  |           |          |                     |                     |                         |                           |                      |                              |
|     |       |           |          |                     |                     |                         |                           |                      | TT-Pin1 — open               |
|     | GND   |           |          | I/O-1: Pin 5        | I/O-1: Pin 5        | I/O-1: Pin 5            | I/O-1: Pin 5              |                      | I/O-1: Pin 5                 |
|     | Vselect1 |        |          | I/O-1: Pin 6        | I/O-1: Pin 6        | I/O-1: Pin 6            | I/O-1: Pin 6              |                      | I/O-1: Pin 6                 |
|     | GND   |           |          |                     |                     | I/O-2: Pin 5            |                           |                      | I/O-2: Pin 5                 |
|     | Vselect2 |        |          |                     |                     | I/O-2: Pin 6            |                           |                      | I/O-2: Pin 6                 |

## Software

### Example Node Applications

- <https://github.com/bocabob/LCC_RPiPico_Turntable>
- <https://github.com/bocabob/LCC_RPiPico_Roundhouse>
- <https://github.com/bocabob/LCC_RPiPico_PixelLights>

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
