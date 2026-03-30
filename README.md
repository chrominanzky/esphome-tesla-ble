# ESPHome Tesla BLE — LAN/Ethernet Overlay

[![GitHub Release][releases-shield]][releases]
[![GitHub Activity][commits-shield]][commits]
[![Last Commit][last-commit-shield]][commits]
[![Platform][platform-shield]](https://github.com/esphome)

This is a **thin LAN/Ethernet overlay** for [PedroKTFC/esphome-tesla-ble](https://github.com/PedroKTFC/esphome-tesla-ble). It replaces WiFi with Ethernet so you can use boards like the [Olimex ESP32-PoE](https://www.olimex.com/Products/IoT/ESP32/ESP32-POE/open-source-hardware).

**All Tesla BLE logic (C++ components, sensors, controls) comes directly from PedroKTFC and auto-updates.** This repo only contains the three small YAML files that swap WiFi for Ethernet.

## How it works

| Source | What it provides |
| --- | --- |
| **PedroKTFC/esphome-tesla-ble** | C++ components (`external_components`), `client.yml` (all sensors & controls), language packs, listener |
| **This repo (chrominanzky)** | `base.yml` (no WiFi, points components to PedroKTFC), `common.yml` (ethernet_info instead of wifi_signal), `project.yml` (BLE scan via API events instead of WiFi events), `boards/olimex-esp32-poe.yml`, `evcc.yml` |

When PedroKTFC releases an update (new sensors, bug fixes, etc.), **you get it automatically** — no merge needed.

## Quick Start

1. Copy [`tesla-ble.example.yml`](./tesla-ble.example.yml) and adapt it
2. Add your board's Ethernet config (e.g. Olimex ESP32-PoE — see example)
3. Create `secrets.yaml` with `ble_mac_address`, `tesla_vin`, `ota_password`, `api_encryption_key`
4. Build & flash via ESPHome Device Builder

For full documentation on sensors, controls, pairing, and polling parameters, see the [upstream README](https://github.com/PedroKTFC/esphome-tesla-ble#readme).

### IEC 61851 / EVCC Integration

This overlay includes an optional `evcc.yml` package that maps Tesla charging states to IEC 61851 standard states (A/B/C/E/F) for [EVCC](https://evcc.io) integration. Uncomment the `evcc` line in your packages to enable it.

## Supported Boards

| Board | File | Connection |
| --- | --- | --- |
| Olimex ESP32-PoE | `boards/olimex-esp32-poe.yml` | Ethernet (LAN8720) |
| M5Stack AtomS3 | `boards/m5stack-atoms3.yml` | WiFi (use upstream) |
| M5Stack Nano C6 | `boards/m5stack-nanoc6.yml` | WiFi (use upstream) |

## Files in this repo

```
packages/
  base.yml        # ESPHome base config (no WiFi, external_components -> PedroKTFC)
  common.yml      # Ethernet info sensors (replaces WiFi signal sensor)
  project.yml     # BLE scan lifecycle via API events (replaces WiFi events)
  evcc.yml        # Optional IEC 61851 text sensor for EVCC
boards/
  olimex-esp32-poe.yml  # Olimex ESP32-PoE board definition
tesla-ble.example.yml   # Example config for LAN/Ethernet users
```

[commits-shield]: https://img.shields.io/github/commit-activity/y/chrominanzky/esphome-tesla-ble
[commits]: https://github.com/chrominanzky/esphome-tesla-ble/commits/main
[releases-shield]: https://img.shields.io/github/v/release/chrominanzky/esphome-tesla-ble
[releases]: https://github.com/chrominanzky/esphome-tesla-ble/releases
[last-commit-shield]: https://img.shields.io/github/last-commit/chrominanzky/esphome-tesla-ble
[platform-shield]: https://img.shields.io/badge/platform-Home%20Assistant%20&%20ESPHome-blue
