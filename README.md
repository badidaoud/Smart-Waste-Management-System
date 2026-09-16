# Smart Waste Management System

A LoRa-powered sensor that tells you how full a garbage bin actually is, so collection trucks stop guessing and start showing up for bins that actually need it. Four of us built this through Catalyst Entrepreneurship, an optional program, not something we had to do for school, just something we wanted to build.

**Team:** Badi Daoud, Jay S., Kenneth Martinez, Mithuran
**My role:** hardware (PCB placement and routing) and the mechanical enclosure (SolidWorks)

> This is my own record of a project four of us built together. I'm documenting it honestly, including what didn't survive (no firmware source, no recovered presentation), and giving credit where it's due. Not a claim that I built it solo.

## Table of Contents

- [Overview](#overview)
- [Team and Roles](#team-and-roles)
- [System Architecture](#system-architecture)
- [Design Process](#design-process)
- [Hardware](#hardware)
- [Final Product](#final-product)
- [Mechanical and Enclosure Design](#mechanical-and-enclosure-design)
- [My Contributions](#my-contributions)
- [Repository Contents](#repository-contents)
- [Known Gaps](#known-gaps)

## Overview

Most waste collection runs on a fixed schedule instead of actual need, so trucks show up to bins that are half empty and skip ones that are already overflowing. We wanted to fix that with something cheap and low-power that just tells you how full a bin is, over a long-range wireless link, so nobody has to guess.

The idea: a battery-powered ESP32 sits on the bin, reads a capacity sensor, and reports back over LoRa to a receiving USB module plugged into a computer. No WiFi or cell signal needed out at the bin, which matters once you're covering a wide area with bins scattered all over the place.

## Team and Roles

Four of us built this through Catalyst Entrepreneurship:

- **Jay S.** wrote the ESP32 firmware.
- **Kenneth Martinez** was the most experienced of the four of us. He reviewed everyone's work and ended up presenting the project.
- **Mithuran** pitched in on reviewing work when he could, though he was buried in university coursework for most of it.
- **Me (Badi)** handled the hardware: PCB placement and routing, plus the mechanical enclosure.

## System Architecture

![System block diagram](assets/block-diagram-system.png)
*How power and signal move around the board: a 3.3V rail feeds the LoRa transmitter and the ESP32, a separate 5V rail runs through a TLV regulator into USB, and the ESP32 talks to the LoRa transmitter, which reports wirelessly to a receiving LoRa USB module plugged into a computer.*

## Design Process

Before any of this was a schematic, it was a napkin sketch:

![First rough concept sketch](assets/block-diagram-first-sketch.png)
*My first pass at it: USB-C charging a battery, running through a nano power timer and voltage regulator to power the ESP32, which drives the LoRa transmitter on one side and reads sensor input through I/O on the other.*

I'd never touched KiCad before this project. Learned it from scratch in under 20 days, just enough to turn that sketch into an actual board: placing components, wiring everything up, and figuring out the antenna keep-out zone the LoRa module needed.

## Hardware

The board runs on an ESP32-WROOM-32D talking to a LoRa transceiver (SX1278), with a BQ24072 handling battery charging, an FTDI chip for programming over USB, and a handful of switches and DIP pins for configuration. The full KiCad project is in [`hardware/kicad/`](hardware/kicad/), routed PCB layout included, not just pictures of it:

| File | Sheet | Contents |
|---|---|---|
| [`LoRa_schematic.kicad_sch`](hardware/kicad/LoRa_schematic.kicad_sch) | Root | ESP32-WROOM-32D, SX1278 LoRa transceiver, antenna, and configuration switches |
| [`untitled.kicad_sch`](hardware/kicad/untitled.kicad_sch) | USB in | USB-C input, MCP73831 Li-ion charger, TPS61200 boost converter, PMOS load switch |
| [`TLV.kicad_sch`](hardware/kicad/TLV.kicad_sch) | TLV (Regulator) | TLV75801 LDO regulator, FT231XQ USB-UART bridge for programming, status LED |
| [`power_block.kicad_sch`](hardware/kicad/power_block.kicad_sch) | Power block | BQ24072 battery management/charging, XC6220 3.3V regulator, thermistor |
| [`LoRa_schematic.kicad_pcb`](hardware/kicad/LoRa_schematic.kicad_pcb) | — | The routed PCB layout |
| [`LoRa_schematic.kicad_pro`](hardware/kicad/LoRa_schematic.kicad_pro) | — | KiCad project file tying the sheets together |

Here's what the layout actually looked like while I was working through it in the PCB editor:

<table>
<tr>
<td><img src="assets/pcb-placement-1.png" alt="PCB component placement, early layout" width="480"/></td>
<td><img src="assets/pcb-placement-2.png" alt="PCB component placement, reorganized layout" width="480"/></td>
</tr>
<tr>
<td align="center"><em>First pass: components are down but nothing's routed yet, hence all the crisscrossing ratsnest lines</em></td>
<td align="center"><em>Second pass: regrouped everything to shorten the routing paths</em></td>
</tr>
</table>

![PCB placement, detail view with antenna keep-out zone](assets/pcb-placement-3.png)
*Zoomed in on the charging circuit (MCP73831), the boost converter (TPS61070), the battery footprint, and the keep-out zone the LoRa antenna needs, no copper or components allowed in that hatched area.*

## Final Product

<table>
<tr>
<td><img src="assets/pcb-3d-bare-copper.png" alt="Bare PCB, copper only" width="480"/></td>
<td><img src="assets/pcb-3d-top.png" alt="Populated PCB, top view" width="480"/></td>
</tr>
<tr>
<td align="center"><em>Bare copper, nothing populated yet</em></td>
<td align="center"><em>Populated: battery holder, ESP32, LoRa module, buttons, status LED</em></td>
</tr>
<tr>
<td><img src="assets/pcb-3d-angle-1.png" alt="Populated PCB, alternate angle" width="480"/></td>
<td><img src="assets/pcb-3d-angle-2.png" alt="Populated PCB, isometric angle" width="480"/></td>
</tr>
<tr>
<td align="center"><em>Same board, different angle</em></td>
<td align="center"><em>And the isometric view, because it looks cool</em></td>
</tr>
</table>

## Mechanical and Enclosure Design

The sensor needed somewhere to actually live on the bin, so I modeled an enclosure in SolidWorks. Source files are in [`mechanical/`](mechanical/):

| File | Description |
|---|---|
| [`1_bin_lid.SLDPRT`](mechanical/1_bin_lid.SLDPRT) | Bin lid part |
| [`1_slot_bin.SLDPRT`](mechanical/1_slot_bin.SLDPRT) | Individual slot bin part |
| [`bins_combined_with_lids.SLDASM`](mechanical/bins_combined_with_lids.SLDASM) | Assembly combining multiple bins with their lids |
| [`outside_box.SLDPRT`](mechanical/outside_box.SLDPRT) | Outer enclosure box |

No renders survived for these, just the source files. Open them up in SolidWorks (or a compatible viewer) if you want to poke around.

## My Contributions

- Took our rough hand-sketched idea and actually built it out in KiCad, schematic and PCB layout, having never opened the program before this project.
- Placed and routed the whole board: the ESP32-WROOM-32D, the SX1278 LoRa transceiver, the BQ24072 battery management circuit, the TPS61070 boost regulator, the FTDI programming interface, and the antenna keep-out zone the LoRa module needs.
- Designed the physical enclosure in SolidWorks: the bin lid, the slot bin, the combined multi-bin assembly, and the outer box.

## Repository Contents

```
.
├── README.md
├── assets/                              Figures used in this README
├── hardware/
│   └── kicad/                            Full KiCad project: schematic sheets and routed PCB layout
└── mechanical/                          SolidWorks source files for the enclosure
    ├── 1_bin_lid.SLDPRT
    ├── 1_slot_bin.SLDPRT
    ├── bins_combined_with_lids.SLDASM
    └── outside_box.SLDPRT
```

## Known Gaps

Being straight about what's not here:

- Jay wrote and flashed the firmware straight from his own laptop when we met up, so there's no firmware source to include.
- The presentation got deleted by a teammate at some point, and nobody's found a copy since.
