# OpenRX Gemini

Open-source dual-radio, dual-band ExpressLRS receiver for FPV aircraft. OpenRX
Gemini combines an ESP32-C3 with two LR1121 radio chains and two U.FL antenna
interfaces for Gemini and Xrossband operation.

<p>
<img src="images/front.png" width="400" alt="OpenRX Gemini front" />
<img src="images/back.png" width="400" alt="OpenRX Gemini back" />
</p>

[![Status](https://img.shields.io/endpoint?url=https://opendrone.be/api/status/OpenRX-Gemini.json)](https://github.com/OpenDrone-hw/.github/blob/main/CONTRIBUTING.md#the-life-of-a-project)
[![Shop](https://img.shields.io/badge/shop-opendrone.be-ffb700)](https://opendrone.be/products/openrx)
[![Discord](https://img.shields.io/badge/Discord-%23receivers-5865F2?logo=discord&logoColor=white)](https://discord.com/channels/1494019459822653512/1494758332903456969)
[![Video](https://img.shields.io/badge/YouTube-How%20ExpressLRS%20Receivers%20Work-FF0000?logo=youtube&logoColor=white)](https://www.youtube.com/watch?v=ssmQkRkXE84)
[![OSHWA](https://img.shields.io/badge/OSHWA-BE000033-0099b0)](https://certification.oshwa.org/be000033.html)

## Specifications

| | |
|---|---|
| Band | 2.4 GHz and sub-GHz, Xrossband |
| Radios | 2x LR1121 |
| Antennas | 2x U.FL |
| Telemetry power | Up to 22 dBm (158 mW) |
| Protocol | CRSF |
| MCU | ESP32-C3 |
| Input | 5 V pad |
| Firmware | ExpressLRS |
| Flashing | Betaflight passthrough or Wi-Fi |
| Dimensions | 17.0 x 15.7 mm |
| PCB | 6-layer, 1.0 mm |

Technical write-up, part list and layout constraints: [AGENTS.md](AGENTS.md).

## In the line

What pairs with what, and what is available:
[opendrone.be](https://opendrone.be).

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md).

## License

Hardware licensed under [CERN-OHL-S-2.0](https://ohwr.org/cern_ohl_s_v2.txt),
see [LICENSE](LICENSE).
