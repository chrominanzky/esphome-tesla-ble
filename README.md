# ESPHome Tesla BLE — LAN/Ethernet Overlay

[![GitHub Release][releases-shield]][releases]
[![GitHub Activity][commits-shield]][commits]
[![Last Commit][last-commit-shield]][commits]
[![Platform][platform-shield]](https://github.com/esphome)

This is a **LAN/Ethernet overlay** for [PedroKTFC/esphome-tesla-ble](https://github.com/PedroKTFC/esphome-tesla-ble). It replaces WiFi with Ethernet so you can use boards like the [Olimex ESP32-PoE](https://www.olimex.com/Products/IoT/ESP32/ESP32-POE/open-source-hardware).

**Sensors, controls, and the listener component come directly from PedroKTFC and auto-update.** This repo additionally hosts a patched `tesla_ble_vehicle` component with improved error recovery (e.g. automatic `ERROR_INCORRECT_EPOCH` handling).

## How it works

| Source | What it provides |
| --- | --- |
| **PedroKTFC/esphome-tesla-ble** | `client.yml` (all sensors & controls), `tesla_ble_listener` component, language packs |
| **This repo (chrominanzky)** | `tesla_ble_vehicle` component (patched for epoch error recovery), `base.yml`, `common.yml` (ethernet_info), `project.yml` (BLE scan via API events), `boards/olimex-esp32-poe.yml`, `evcc.yml` |

When PedroKTFC releases an update (new sensors, bug fixes, etc.), **you get it automatically** for `client.yml` and the listener component — no merge needed.

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

## Migrating from the old config format

If you are upgrading from an older version that used individual `github://` package references and inline `esphome:`, `esp32:`, `ethernet:` blocks, follow these steps:

1. **Remove** the `esphome:`, `esp32:`, `logger:`, and `ota:` top-level blocks from your YAML — they are now provided by `base.yml`.
2. **Remove** the inline `ethernet:` block — it is now provided by your board package (e.g. `boards/olimex-esp32-poe.yml`).
3. **Replace** the old single-file package references:
   ```yaml
   # OLD format — remove these:
   packages:
     base:    github://chrominanzky/esphome-tesla-ble/packages/base.yml
     common:  github://chrominanzky/esphome-tesla-ble/packages/common.yml
     project: github://chrominanzky/esphome-tesla-ble/packages/project.yml
     client:  github://chrominanzky/esphome-tesla-ble/packages/client.yml
   ```
   ```yaml
   # NEW format — use this instead:
   packages:
     lan_overlay:
       url: https://github.com/chrominanzky/esphome-tesla-ble/
       path: packages
       files: [base.yml, common.yml, project.yml]
       refresh: 0s
     upstream:
       url: https://github.com/PedroKTFC/esphome-tesla-ble/
       path: packages
       files: [client.yml]
       refresh: 0s
     board: github://chrominanzky/esphome-tesla-ble/boards/olimex-esp32-poe.yml
   ```
4. **Set your device name** via substitutions instead of the `esphome:` block:
   ```yaml
   substitutions:
     device_name: tessyble        # was esphome: name:
     friendly_name: Tessy BLE     # was esphome: friendly_name:
   ```
5. Keep your `api:` encryption and `substitutions` for `ble_mac_address`, `tesla_vin`, etc.

See [`tesla-ble.example.yml`](./tesla-ble.example.yml) for a complete working example.

## Files in this repo

```
components/
  tesla_ble_vehicle/  # Patched vehicle component (epoch error recovery)
packages/
  base.yml        # ESPHome base config (no WiFi, external_components split)
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
