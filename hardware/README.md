# Wireless Keyball39 hardware plan

[简体中文版](README.zh-CN.md)

This directory describes a buildable first revision of the wireless Keyball39
used by the ZMK configuration in this repository.

For fabrication and marketplace search terms in mainland China, see
[`china-sourcing.md`](china-sourcing.md).

## Architecture

Use the original Keyball39 Rev1 PCB and plate geometry as the mechanical and
key-matrix base. Replace the two Pro Micro controllers with socketed
nice!nano v2 controllers, omit the TRRS link and RGB LEDs, and replace the
PMW3360 sensor board with a 3.3 V PMW3610 breakout.

This works because the original PCB and this firmware use the same Pro Micro
pins for the matrix and OLED:

| Function | Pro Micro label | nice!nano GPIO |
| --- | --- | --- |
| Column 0..5 | D4, D5, D6, D7, D8, D9 | P0.22, P0.24, P1.00, P0.11, P1.04, P1.06 |
| Row 0..3 | D21, D20, D19, D18 | P0.31, P0.29, P0.02, P1.15 |
| OLED SDA | D2 | P0.17 |
| OLED SCL | D3 | P0.20 |

The right half is the ZMK split central and contains the trackball. Each half
has its own battery and power switch; the halves communicate over BLE. Do not
fit or connect a TRRS cable.

## Source designs

The original project's `keyball39/design_data` directory contains editable
KiCad projects, production Gerbers, and acrylic DXFs:

<https://github.com/Yowkees/keyball/tree/main/keyball39/design_data>

It also contains printable case STLs:

<https://github.com/Yowkees/keyball/tree/main/keyball39/3D_Printer_STL>

Those files are GPLv3. Preserve attribution and the GPLv3 terms when
redistributing modified versions.

Use a fully assembled and function-tested PMW3610 breakout. It must include the
PMW3610 sensor, LM18-LSI lens, and all support components; a bare PCB or a loose
sensor-and-lens kit is not acceptable for this build. A populated version of
the compact ufan design is preferred because its approximately 17 x 24.7 mm
outline and recommended 1 mm PCB are better suited to the original Keyball's
vertical sensor-board envelope than larger general-purpose boards:

<https://github.com/ufan/pmw3610_breakout>

The seller must confirm 3.3 V operation and expose VIN, GND, SCLK, SDIO, nCS,
and MOTION. Obtain a dimensioned drawing and pinout before ordering. The exact
module is not mechanically interchangeable with other PMW3610 breakouts, so
the holder must not be finalized until the purchased module is identified.
The breakout remains a separate board in revision 1 so the keyboard PCB does
not need to be redesigned.

## PMW3610 wiring

The firmware assignments are defined in
`config/boards/shields/keyball_nano/keyball39_right.overlay`.

| PMW3610 breakout | nice!nano pin | nRF52840 GPIO | Original PCB signal |
| --- | --- | --- | --- |
| VIN | VCC | 3.3 V | VCC |
| GND | GND | GND | GND |
| SCLK | D15 | P1.13 | SCLK |
| SDIO | D16 | P0.10 | MOSI |
| nCS | D10 | P0.09 | NCS |
| MOTION | D14 | P1.11 | MISO |

MOSI and MISO deliberately use the same P0.10 pin in the overlay because the
PMW3610 uses three-wire SPI. `MOTION` is a separate interrupt input on P1.11.
The firmware does not use the sensor's RESET signal; configure the breakout's
RESET jumper according to its documentation.

On the original ball-side PCB, the 7-pin sensor connector can carry these
signals after selecting the ball-right jumper routing:

| 7-pin connector | Revision 1 use |
| --- | --- |
| 1 | MOTION (the PCB net is labelled MISO) |
| 2 | SDIO (the PCB net is labelled MOSI) |
| 3 | GND |
| 4 | 3.3 V VCC |
| 5 | GND |
| 6 | nCS |
| 7 | SCLK |

Before connecting a sensor, use continuity mode to verify every connector pin
against the nice!nano pin shown above. The original PCB is reversible and its
jumper choice determines the connector routing, so silkscreen names alone are
not a sufficient check.

## Power

Use one protected 3.7 V, 100-110 mAh 301230 LiPo per half for the first build.
Mount each controller in machine-pin sockets so the cell can sit below it
without contacting sharp pins. Put a physical switch in series with the
battery positive lead. Connect the switched lead to nice!nano `B+` and the
battery negative lead to `B-`.

Charge each half through its own nice!nano USB-C connector. Never connect a
battery directly to `RAW`, `VCC`, or a GPIO. Inspect polarity with a multimeter
before inserting a controller. Do not use a swollen, punctured, unprotected,
or reverse-polarity cell.

OLEDs reduce battery life. They should remain fitted for compatibility with
the current firmware, but can later be disabled in firmware if runtime matters
more than the displays.

## Mechanical trackball module

The PMW3360 holder from the original build cannot be assumed to position a
PMW3610 correctly. Revision 1 therefore needs a custom printed adapter/cup
with all of the following verified before final assembly:

- a 34 mm ball supported at three points by 2 mm ceramic bearings;
- the LM18-LSI lens centered on the ball's lowest point;
- the breakout board held square to that optical axis;
- lens-to-ball spacing matching the breakout and lens documentation;
- clearance for the PCB, connector, wires, and ball removal;
- attachment to the two existing Keyball39 trackball mounting holes without
  loading the sensor board.

Do not order a production batch of this printed part before testing one
prototype. Sensor height is the dimension most likely to require iteration.

## Build order

1. Order one set of original Keyball39 middle/top PCBs and plates using the
   upstream production files, plus the parts in `bom.csv`.
2. Assemble only diodes, hot-swap sockets, reset switches, controller sockets,
   OLED sockets, battery leads, and power switches. Omit RGB and TRRS parts.
3. Flash both nice!nanos before installing batteries.
4. Test each matrix half independently by shorting switch sockets.
5. Pair the BLE split and test all 39 keys.
6. Wire the PMW3610 breakout through the right PCB's 7-pin connector and test
   pointer motion with the breakout safely supported on the bench.
7. Prototype and adjust the trackball adapter.
8. Install switches, keycaps, batteries, and bottom covers only after all
   electrical tests pass.

## Firmware flashing

Firmware is built by GitHub Actions, not locally. The workflow produces left,
right, and settings-reset UF2 files. Double-tap a nice!nano reset button to
mount its bootloader drive, then copy the matching UF2 to it.

Flash order for a new pair:

1. Flash `settings_reset` to both halves.
2. Flash the left UF2 to the left controller.
3. Flash the right UF2 to the right controller.
4. Power-cycle both halves, then pair the right half with the host.

Do not attach a battery or sensor until controller orientation, power-rail
continuity, and the absence of shorts have been checked.
