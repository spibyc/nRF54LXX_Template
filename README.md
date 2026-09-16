# nRF54LXX Hardware Template (QFN-48)

Reusable KiCad schematic and PCB layout template for designs based on the Nordic Semiconductor **nRF54L15 / nRF54L10 / nRF54L05** family.

The purpose of this repository is to provide a known starting point for new nRF54L designs rather than recreating the MCU support circuitry, RF section, programming interface, and component selection for every project.

For a new design, copy this template and build the application-specific circuitry around it.

## Overview

The template currently uses the **nRF54L15-QFAA-R7** as the populated MCU and includes the core circuitry required around the device.

The PCB is already laid out so that the placement and routing of the critical MCU, clock, power, and RF components can be reused between projects.

Included in the template:

- nRF54L15-QFAA-R7 MCU
- 32 MHz high-frequency crystal and associated components
- 32.768 kHz low-frequency crystal
- MCU supply decoupling
- Internal 0.9V regulator components
- RF matching network
- BLE RF routing
- Murata MM8130-2600RA2 RF connector
- SWD programming/debug interface
- Tag-Connect TC2050 programming footprint
- MCU reset connection
- Populated BOM for the template components

## Repository Contents

```text
nRF54LXX.kicad_sch    KiCad template schematic
nRF54LXX.kicad_pcb    KiCad PCB layout
BOM.csv               Template bill of materials
README.md             Project documentation
```

## Intended Use

This project is intended to act as a **master template** for future nRF54L-based hardware.

When starting a new product:

1. Copy the schematic and PCB files into the new project.
2. Rename the KiCad project as required.
3. Retain the MCU support, RF, clock, power, and programming sections.
4. Add the application-specific peripherals, power supplies, sensors, drivers, connectors, etc.
5. Route the new circuitry around the existing nRF54L layout.
6. Review the final design against the requirements of the exact nRF54L device being fitted.

The main advantage of this approach is that the sensitive RF and MCU support circuitry does not need to be recreated from scratch for every design.

## RF / BLE Section

The template includes the RF circuitry and PCB layout for the Bluetooth Low Energy interface.

The populated RF network includes:

| Reference | Value |
|---|---:|
| L2 | 2.7 nH |
| L3 | 3.5 nH |
| L4 | 3.5 nH |
| C9 | 3.9 pF |
| C10 | 1.5 pF |
| C11 | 2 pF |
| C12 | 0.3 pF |
| C13 | 1.4 pF |
| R1 | 0 Ω |

A **Murata MM8130-2600RA2** RF connector is included as J1.

The RF component placement and routing in the PCB file should be treated as a critical part of the template. Avoid moving RF components or changing RF trace geometry unless the RF design is being deliberately re-evaluated.

> **Important:** Copying this layout does not remove the need for RF verification on the final product. Changes to the PCB stack-up, ground structure, enclosure, antenna, nearby components, or mechanical construction can affect RF performance.

## Clocking

Two external crystals are included.

### 32 MHz HF Crystal

**Y2 — 32 MHz, 2016 4-pin package**

The 32 MHz crystal and its surrounding components are positioned close to the MCU to minimise trace length and interference.

### 32.768 kHz LF Crystal

**Y1 — 32.768 kHz, 2012 2-pin package**

The low-frequency crystal is provided for applications requiring the external 32.768 kHz clock source.

If the LF crystal is not required by a particular application, its use should be reviewed against the firmware configuration and Nordic documentation before removing it.

## Programming and Debug

The template includes the connections required for SWD programming and debugging.

Signals provided include:

- `SWDIO`
- `SWDCLK`
- `MCU_RST`
- Power/reference connections required by the programming interface

J2 uses a **Tag-Connect TC2050** style footprint, allowing production programming and development access without fitting a permanent header to the finished PCB.

This section is intended to remain in derived designs so that new boards have a consistent programming interface.

## Internal regulator and Decoupling

The template includes the local supply filtering, decoupling, and internal 0.9V regulator support components used around the MCU.

Current BOM values include:

| Reference | Value |
|---|---:|
| C1 | 10 µF |
| C2–C5 | 100 nF |
| C6 | 10 nF |
| C7–C8 | 2.2 µF |
| L1 | 4.7 µH |
| FB1 | 120 Ω / 0.2 A ferrite bead |

These components are already placed around the MCU in the PCB layout.

Their placement is intentional. In derived projects, avoid moving decoupling and power components away from the MCU simply to make application routing easier.

## MCU

The current template is populated with:

**Nordic Semiconductor nRF54L15-QFAA-R7**

The template is intended for use across compatible members of the nRF54L family, including:

- nRF54L15
- nRF54L10
- nRF54L05

When changing MCU variant, verify pin compatibility, available peripherals, memory configuration, package requirements, power configuration, and firmware target before manufacturing the board.

Do not assume that every nRF54L variant is electrically interchangeable without checking the relevant Nordic documentation.

## Bill of Materials

`BOM.csv` contains the components currently used by the template, including their references, quantities, values, and PCB footprints.

The current template contains the following major parts:

| Component | Description |
|---|---|
| IC1 | nRF54L15-QFAA-R7 |
| J1 | Murata MM8130-2600RA2 RF connector |
| J2 | Tag-Connect TC2050 programming interface |
| Y1 | 32.768 kHz crystal |
| Y2 | 32 MHz crystal |
| L1 | 4.7 µH power inductor |
| FB1 | 120 Ω / 0.2 A ferrite bead |
| L2–L4 | RF inductors |
| C1–C13 | Power, clock and RF capacitors |
| R1 | 0 Ω RF component |

The BOM should be copied into each derived project and updated as application-specific components are added.

## PCB Layout Guidance

Several parts of this layout should be considered **template-controlled / critical circuitry**.

In particular, avoid unnecessary modification of:

- MCU decoupling placement
- MCU power components
- 32 MHz crystal placement and routing
- RF matching network placement
- RF routing
- RF ground structure
- SWD programming connections

Application circuitry can then be added around this core layout.

If the board stack-up or RF implementation changes substantially, the RF section should be reviewed rather than assumed to remain valid.

## Design Philosophy

The goal of this repository is simple:

> **Do the critical nRF54L implementation once, then reuse it.**

A new product should not require rebuilding the MCU support circuitry from a blank schematic.

Instead, this repository provides a common starting point containing the circuitry that is likely to remain largely unchanged between designs:

```text
                 ┌─────────────────────────┐
                 │       nRF54LXX MCU      │
                 │                         │
                 │   Power / Decoupling    │
                 │   HF/LF Clocking        │
                 │   SWD Programming       │
                 │   BLE RF Interface      │
                 └────────────┬────────────┘
                              │
                         Application
                           Circuitry
```

This reduces repeated design work and helps maintain consistency between nRF54L-based products.

## Important Notes

This repository is a **design template**, not a substitute for final design verification.

Before releasing a derived PCB for manufacture:

- Verify the exact MCU variant being used.
- Review the latest Nordic reference circuitry and documentation.
- Run KiCad ERC and DRC.
- Verify all footprints against the selected manufacturer part numbers.
- Confirm the PCB stack-up.
- Review controlled/critical RF geometry.
- Verify power integrity and decoupling.
- Verify SWD programming on assembled hardware.
- Verify both clock sources where used.
- Perform RF testing on the final assembled product.
- Update the BOM for the final product.

Any changes to the RF path, matching components, antenna implementation, PCB stack-up, or surrounding mechanical design should be treated as potentially affecting RF performance.

## Status

This repository is intended to evolve as the nRF54L implementation is validated and reused in future designs.

Improvements to the common MCU circuitry should be made to the master template so that future projects inherit the latest validated implementation.
