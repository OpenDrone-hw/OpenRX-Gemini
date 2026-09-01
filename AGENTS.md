# OpenRX Gemini

Open-source dual-radio, dual-band ExpressLRS receiver. An ESP32-C3 controls two
LR1121 radio chains, each with its own U.FL antenna interface, for Gemini and
Xrossband operation. A separate ceramic antenna serves ESP32-C3 Wi-Fi.

## Repo

| | |
|---|---|
| Maintainer | @bastian2001 |
| Status | See the `status-*` topic on the repo. Never written here. |
| Designed in | KiCad 10 |
| KiCad project | `hardware/OpenRX-Gemini.kicad_pro` |
| Root schematic | `hardware/OpenRX-Gemini.kicad_sch`; sheets `esp32-c3.kicad_sch`, `clock.kicad_sch`, and two instances of `lr1121.kicad_sch` |
| Board | `hardware/OpenRX-Gemini.kicad_pcb`, 6 layers, 1.0 mm |
| Local library | `hardware/lib.pretty/` and `hardware/lib.3dshapes/`, nickname `OpenRX-Shared` retained by the design; `shared/libs/OpenRX-Shared.3dshapes` is a compatibility link for embedded legacy model paths |
| Shared library | `hardware/KiCad-Library/`, submodule of [OpenDrone-hw/KiCad-Library](https://github.com/OpenDrone-hw/KiCad-Library), nickname `OpenDrone`; exact component datasheets resolve through the project text variable `OPENDRONE_LIB` |
| Design rules | `hardware/OpenRX-Gemini.kicad_dru` |
| Fab config | `hardware/fabrication-toolkit-options.json` |
| Firmware target | `firmware/OpenRX Gemini LR1121.json` and `firmware/targets_entries.json` |
| Board setup | Standard: 6 layers, 0.09 mm clearance and track, via 0.35 on 0.20 drill |
| License | CERN-OHL-S-2.0 |

## Parts and datasheets

- **Per-repository part index:** `hardware/OpenRX-Gemini.kicad_sch` and its
  listed sub-sheets are authoritative for what this board fits. Export the
  netlist with the command below when a script-readable board index is needed;
  do not maintain a second hand-written BOM.
- **Proven shared parts:**
  `hardware/KiCad-Library/PARTS-USED.md` is the catalogue index. Its `Boards`
  column identifies every repository using each LCSC part; filter it for
  `OpenRX-Gemini` to get this repository's proven shared-part view.
- **Exact datasheets:**
  `hardware/KiCad-Library/datasheet/manifest.json` maps shared symbols to the
  committed PDFs and their SHA-256 hashes. The PDFs live in
  `hardware/KiCad-Library/datasheet/`, and symbol links resolve there through
  `OPENDRONE_LIB`.
- **Local-only parts:** inspect `hardware/lib.pretty/` and
  `hardware/lib.3dshapes/`, then verify supplier fields in the board
  schematic. Do not duplicate a shared part or datasheet.

## Rules

Identical in every OpenDrone board repo. Do not edit here; edit the template.

- **Never text-edit** `.kicad_sch`, `.kicad_pcb` or `.kicad_dru`. Use KiCad, or
  kicad-skip / the pcbnew API for scripted changes. `.kicad_pro` is JSON and may
  be edited directly for metadata.
- **Metadata yes, connections no.** An agent may write BOM and documentation
  fields (MPN, Manufacturer, LCSC, Cost, Datasheet, text variables). An agent
  may not change nets, wiring, routing, placement, footprint assignment, or any
  value that changes the circuit.
- **Close KiCad before any write to a KiCad file.** KiCad caches library tables
  at process start and overwrites files on save.
- **Reuse before you draw.** Check the `OpenDrone` library and its
  `PARTS-USED.md` first. If the part is there we have already sourced,
  footprinted and shipped it, and its symbol links to the exact committed
  datasheet: place it from `OpenDrone`. Draw a new part into `lib` only when
  the catalogue has nothing that fits, imported with `easyeda2kicad` from its
  LCSC number. Pulling a newer catalogue is a deliberate, reviewed commit:
  `git submodule update --remote hardware/KiCad-Library`, then DRC.
- **One person holds a board layout at a time.** KiCad files do not merge. Say
  on Discord that you are taking it. See [CONTRIBUTING.md](CONTRIBUTING.md).
- **Run ERC and DRC before every pull request.** Existing approved findings
  may remain; a new type or increased count must be reviewed before merge.
  Commands below.

## Environment

```sh
kicad-cli sch erc hardware/OpenRX-Gemini.kicad_sch
kicad-cli pcb drc --schematic-parity --refill-zones hardware/OpenRX-Gemini.kicad_pcb
kicad-cli sch export netlist --format kicadsexpr -o /tmp/OpenRX-Gemini.net hardware/OpenRX-Gemini.kicad_sch
```

On macOS `kicad-cli` is at
`/Applications/KiCad/KiCad.app/Contents/MacOS/kicad-cli`, and `pcbnew` imports
only under KiCad's bundled Python. Reusable scripts for renders, STEP export,
and packaging art come from Incutec hardware tooling. The OpenDrone release
standard lives in `OpenDrone-hw/.github/RELEASES.md`; board-specific scripts
live in `hardware/tools/`.

## Architecture

The ESP32-C3 (U1) runs ExpressLRS, communicates over UART0/CRSF, and controls
both LR1121 radios over a shared SPI bus with separate NSS, reset, busy and
DIO1 signals. A single 32 MHz TCXO feeds both radios. Each radio has the Mono
RF chain: 2.4 GHz passes through a filter and RFX2401C PA/LNA; sub-GHz uses the
LR1121 high-power output and 0900PC16J0042001E IPD; a SKY13373 selects the band
for its U.FL. Radio 1 feeds J1 and radio 2 feeds J2. In dual-band firmware,
radio 1 remains sub-GHz and radio 2 remains 2.4 GHz.

## Key parts

| Function | Ref | Part | LCSC | Note |
|---|---|---|---|---|
| MCU | U1 | ESP32-C3 | | QFN-32 |
| 3.3 V LDO | U2 | TLV75533PDQNR | C2861882 | 500 mA |
| Dual-band radios | U3, U6 | LR1121IMLTRT | C7498014 | Two chains |
| 2.4 GHz PA/LNA | U4, U7 | RFX2401C | C19213 | |
| RF switches | U5, U8 | SKY13373-460LF | C150853 | |
| Sub-GHz IPDs | T1, T2 | 0900PC16J0042001E | C19842466 | |
| 2.4 GHz filters | FL1, FL2 | 2450FM07D0034T | C2651081 | |
| ELRS connectors | J1, J2 | U.FL-R-SMT-1(80) | C88374 | |
| Wi-Fi antenna | AE1 | 2450AT18A100E | C89334 | ESP32-C3 only |
| Shared radio TCXO | OSC1 | OW7EL89CENUYO3YLC-32M | C22381772 | 32 MHz |
| MCU crystal | X1 | CJ17-400001010B20 | C2875272 | 40 MHz |
| Status LED | D1 | XL-1010RGBC-WS2812B | C5349953 | |
| BOOT button | U9 | TS2306A | C2976675 | GPIO9 |

## Power

```text
5V pad (TP3)
└── TLV75533PDQNR (U2), 3.3 V
    ├── ESP32-C3 (U1) and status LED (D1)
    ├── LR1121 radios (U3, U6) and shared 32 MHz TCXO (OSC1)
    └── PA/LNAs (U4, U7) and RF switches (U5, U8)
```

## Connectors and I/O

| Pad | Net | ESP32-C3 | Function |
|---|---|---|---|
| RX (TP1) | U0RXD | GPIO20 | CRSF serial input |
| TX (TP2) | U0TXD | GPIO21 | CRSF telemetry output |
| 5V (TP3) | +5V | - | Supply input |
| GND (TP4) | GND | - | Ground |
| BOOT / U9 | BOOT | GPIO9 | UART download mode and button |

Shared SPI is SCK/MOSI/MISO on GPIO6/4/5. Radio 1 NSS/RST/BUSY/DIO1 are
GPIO0/2/3/1; radio 2 uses GPIO7/10/8/18. The RGB LED is GPIO19 in GRB order.

## Firmware

ExpressLRS target `Unified_ESP32C3_LR1121_RX`, platform `esp32-c3`, minimum
version 3.5.0. Upload methods are UART, Wi-Fi and Betaflight passthrough. The
authoritative GPIO, RF-switch table and power values are in
`firmware/OpenRX Gemini LR1121.json`.

## Layout rules

Keep the two RF chains symmetric and isolated. Preserve their
controlled-impedance paths, via fences, exposed-pad via arrays and the Wi-Fi
antenna keepout. The shared TCXO fan-out must remain short and balanced.

## Revisions

| Rev | Date | Change |
|---|---|---|
| rev2 | 2026-08-14 | Fab set re-exported and verified; small batch ordered. |
| rev1 | 2026-03-23 | First released OpenRX Gemini design. |
