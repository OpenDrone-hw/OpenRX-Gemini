# OpenRX Gemini

Open-source dual-radio, dual-band ExpressLRS receiver. An ESP32-C3 controls two
LR1121 radio chains, each with its own U.FL antenna interface, for Gemini and
Xrossband operation. A separate ceramic antenna serves ESP32-C3 Wi-Fi.

## Architecture

The ESP32-C3 (U1) runs ExpressLRS, communicates over UART0/CRSF, and controls
both LR1121 radios over a shared SPI bus with separate NSS, reset, busy and
DIO1 signals. A single 32 MHz TCXO feeds both radios. Each radio has the Mono
RF chain: 2.4 GHz passes through a filter and RFX2401C PA/LNA; sub-GHz uses the
LR1121 high-power output and 0900PC16J0042001E IPD; a SKY13373 selects the band
for its U.FL. Radio 1 feeds J1 and radio 2 feeds J2. In dual-band firmware,
radio 1 remains sub-GHz and radio 2 remains 2.4 GHz.

## Power

```text
5V pad (TP3)
└── TLV75533PDQNR (U2), 3.3 V
    ├── ESP32-C3 (U1) and status LED (D1)
    ├── LR1121 radios (U3, U6) and shared 32 MHz TCXO (OSC1)
    └── PA/LNAs (U4, U7) and RF switches (U5, U8)
```

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

## Layout rules

Keep the two RF chains symmetric and isolated. Preserve their
controlled-impedance paths, via fences, exposed-pad via arrays and the Wi-Fi
antenna keepout. The shared TCXO fan-out must remain short and balanced.

## Firmware

ExpressLRS target `Unified_ESP32C3_LR1121_RX`, platform `esp32-c3`, minimum
version 3.5.0. Upload methods are UART, Wi-Fi and Betaflight passthrough. The
authoritative GPIO, RF-switch table and power values are in
`firmware/OpenRX Gemini LR1121.json`.

## Repo

| | |
|---|---|
| Maintainer | @bastian2001 |
| KiCad project | `hardware/OpenRX-Gemini.kicad_pro` |
| Root schematic | `hardware/OpenRX-Gemini.kicad_sch`; sheets `esp32-c3.kicad_sch`, `clock.kicad_sch`, and two instances of `lr1121.kicad_sch` |
| Board | `hardware/OpenRX-Gemini.kicad_pcb`, 6 layers, 1.0 mm |
| Local library | `hardware/lib.pretty/` and `hardware/lib.3dshapes/`, nickname `OpenRX-Shared` retained by the design; `shared/libs/OpenRX-Shared.3dshapes` is a compatibility link for embedded legacy model paths |
| Design rules | `hardware/OpenRX-Gemini.kicad_dru` |
| Firmware target | `firmware/OpenRX Gemini LR1121.json` and `firmware/targets_entries.json` |

## Rules

The shared [Board rules](https://github.com/OpenDrone-hw/hardware-template/blob/main/AGENTS.md#board-rules) apply. Only what differs for this board is listed here.

## Revisions

| Rev | Date | Change |
|---|---|---|
| rev2 | 2026-08-14 | Fab set re-exported and verified; small batch ordered. |
| rev1 | 2026-03-23 | First released OpenRX Gemini design. |

