---
layout: default
title: Milesight - WS523
parent: Sensors
---

<img src="https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/milesight-ws523_01.png" width="250" align="right" class="inline"/>

# Milesight - WS523
{: .no_toc }

- Manufacturer: <a href="https://www.milesight.com/" target="_blank">Milesight (Xiamen Milesight IoT Co., Ltd.)</a>
- Product: <a href="https://www.milesight.com/iot/product/lorawan-sensor/ws523" target="_blank">WS523 LoRaWAN Smart Portable Socket</a>

The Milesight WS523 is a LoRaWAN® Class C smart plug for remote ON/OFF control and power consumption monitoring of electrical appliances. It supports most international socket types and can be configured via NFC using the Milesight ToolBox App.

---

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Specifications
- Price: on request (ca. CHF 80–120 depending on socket type and supplier)
- Socket Types / Rated Current: EU (Type F) 16 A / UK (Type G) 13 A / US (Type B) 15 A / AU (Type I) 10 A / CN 10 A or 16 A / FR (Type E) 16 A
- Operating Voltage: 100–250 VAC, 50–60 Hz
- Measurement data points:
  - Socket Status (ON/OFF)
  - Voltage (VAC)
  - Current (mA)
  - Active Power (W)
  - Power Consumption (kWh, cumulative)
- Measurement Accuracy: typical ±3 %, maximum ±5 % (±1 % customisable)
- Overload Protection: yes (configurable threshold, audible/visual alarm)
- Power Outage Alert: yes (capacitor-backed)
- Configuration: Milesight ToolBox App via NFC, or LoRaWAN® downlink
- LoRaWAN® Class C device (fixed)
- LoRaWAN® version 1.0.2 / 1.0.3
- Join modes: OTAA and ABP
- Frequency bands: EU868, US915, AU915, AS923, KR920, IN865, RU864, CN470
- Transmission range: up to 2 km urban / 15 km rural (line of sight)
- Supports Milesight D2D protocol (ultra-low latency device-to-device control without gateway)
- No external antenna (internal)
- Operating Temperature Range: –20 °C … +55 °C
- Indoor use only

---

## Documents
- [User Guide WS523 (latest)](https://resource.milesight.com/milesight/iot/document/ws52x-user-guide-en.pdf)
- [Datasheet WS523 (latest)](https://resource.milesight.com/milesight/iot/document/ws52x-datasheet-en.pdf)

---

## Links
- [Payload Decoder on GitHub (Milesight-IoT/SensorDecoders)](https://github.com/Milesight-IoT/SensorDecoders/tree/main/ws-series/ws52x)
- [Milesight ToolBox App (Android / iOS)](https://www.milesight.com/iot/resources/download/toolbox/)

---

## Ordering Info
- EU Type F (868 MHz): `WS523-868M-EU` – WS523 LoRaWAN Smart Portable Socket, Type F (Europe)

---

## Device specific Information

- Select **Class C** when registering the device in TTN
- Default uplink **FPort: 85**
- Default **App EUI (JoinEUI): `24E124C0002A0001`**
- Default **AppKey** (devices shipped from Q4 2025 onwards): DevEUI + DevEUI (e.g. `24e124123456789024e1241234567890`)
  - Older devices: `5572404C696E6B4C6F52613230313823`
- The **Device EUI** is printed on the device label

### Get DevEUI and configure the device
1. Install **Milesight ToolBox** App (Android or iOS) on an NFC-capable smartphone.
2. Enable NFC on the smartphone.
3. Launch ToolBox, hold the smartphone NFC area against the WS523.
4. Read device information – DevEUI, AppKey and all settings are displayed.
5. Configure LoRaWAN settings (JoinEUI, AppKey, frequency, reporting interval) and click **Write** to save.
6. Re-read the device to verify the configuration.
7. Press the power button once to start network join.
   - LED blinks once: join requests being sent
   - LED blinks twice: joined successfully
   - If no join after 32 attempts, LED stops blinking → check gateway/TTN settings

> **Tip:** Remove the phone case if NFC read/write fails.

### Reset to Factory Default
- **Hardware:** Hold the power button for more than 10 seconds until the LED blinks rapidly.
- **ToolBox App:** Go to Device → Maintenance → Reset, then hold the phone to the device.

### Reporting Interval and Measurement Selection
The reporting interval and which values are transmitted are configured via the ToolBox App (NFC) or via a LoRaWAN downlink command to the device.

**Downlink – Socket Control (FPort 85)**

| Command | Hex |
|---|---|
| Switch ON | `08 FF` |
| Switch OFF | `08 00` |

**Downlink – Set Reporting Interval (FPort 85)**

| Interval | Hex |
|---|---|
| 1 minute  | `03 00 3C 00` |
| 5 minutes | `03 00 2C 01` |
| 10 minutes | `03 00 58 02` |
| 15 minutes | `03 00 84 03` |
| 60 minutes | `03 00 10 0E` |

> Refer to the [User Guide](https://resource.milesight.com/milesight/iot/document/ws52x-user-guide-en.pdf) Chapter 6 for the full downlink command reference.

---

## Uplink Payload Format

All uplink data is sent on **FPort 85** in little-endian byte order using Milesight's channel-type encoding.
Each value is encoded as: `[Channel Byte] [Type Byte] [Data Bytes]`

### Periodic Report

| Channel | Type | Bytes | Measurement | Unit | Factor |
|---|---|---|---|---|---|
| `0x03` | `0x74` | 2 | Voltage | V | / 10 |
| `0x04` | `0x74` | 2 | Current | mA | 1 |
| `0x05` | `0x74` | 2 | Active Power | W | 1 |
| `0x06` | `0x74` | 1 | Power Factor | % | 1 |
| `0x07` | `0xC8` | 4 | Power Consumption | kWh | / 1000 |
| `0x08` | `0x70` | 1 | Socket Status | – | 0x00=off, 0x01=on |

### Socket Change Report
Sent immediately when the socket state changes (ON/OFF).

### Power Outage Alert
Sent when mains power is lost (capacitor-backed).

---

##  Payload Formatter

The official decoder is maintained by Milesight on GitHub:  
 **[https://github.com/Milesight-IoT/SensorDecoders/tree/main/ws-series/ws52x](https://github.com/Milesight-IoT/SensorDecoders/tree/main/ws-series/ws52x)**


```javascript
/**
 * Milesight WS52x LoRaWAN Smart Portable Socket
 * Payload Decoder for The Things Network (TTN v3)
 *
 * Source: https://github.com/Milesight-IoT/SensorDecoders
 * License: see repository
 *
 * Uplink port: 85
 * Byte order:  little-endian
 *
 * Example decoded output:
 * {
 *   "socket":            "open",   // open = ON, close = OFF
 *   "voltage":           231.4,    // V
 *   "current":           0.824,    // A
 *   "active_power":      187,      // W
 *   "power_factor":      98,       // %
 *   "power_consumption": 14.036,   // kWh (cumulative)
 *   "power_outage":      true,     // present only on power outage alert
 * }
 */
function decodeUplink(input) {
    var bytes = input.bytes;
    var decoded = {};

    for (var i = 0; i < bytes.length; ) {
        var channel = bytes[i++];
        var type    = bytes[i++];

        if (channel === 0x03 && type === 0x74) {
            // Voltage: UINT16, unit 0.1 V → V
            decoded.voltage = (bytes[i] | (bytes[i + 1] << 8)) / 10;
            i += 2;
        } else if (channel === 0x04 && type === 0x74) {
            // Current: UINT16, unit mA → A
            decoded.current = (bytes[i] | (bytes[i + 1] << 8)) / 1000;
            i += 2;
        } else if (channel === 0x05 && type === 0x74) {
            // Active Power: INT16, unit W
            var raw = bytes[i] | (bytes[i + 1] << 8);
            decoded.active_power = (raw & 0x8000) ? raw - 0x10000 : raw;
            i += 2;
        } else if (channel === 0x06 && type === 0x74) {
            // Power Factor: UINT8, unit %
            decoded.power_factor = bytes[i];
            i += 1;
        } else if (channel === 0x07 && type === 0xC8) {
            // Power Consumption: UINT32, unit Wh → kWh
            var wh = (bytes[i] | (bytes[i+1] << 8) | (bytes[i+2] << 16) | (bytes[i+3] << 24)) >>> 0;
            decoded.power_consumption = wh / 1000;
            i += 4;
        } else if (channel === 0x08 && type === 0x70) {
            // Socket Status: 0x00 = close (OFF), 0x01 = open (ON)
            decoded.socket = bytes[i] === 0x00 ? "close" : "open";
            i += 1;
        } else if (channel === 0x09 && type === 0x70) {
            // Power Outage Alert: 0x00 = power restored, 0x01 = power outage
            decoded.power_outage = bytes[i] !== 0x00;
            i += 1;
        } else {
            // Unknown channel/type – skip 1 byte and attempt to resync
            i += 1;
        }
    }

    return { data: decoded };
}
```

---

## Adapted Payload Formatter HSLU

The following decoder uses HSLU field naming conventions and covers all relevant uplink types:
- Periodic report (voltage, current, power, energy)
- Socket change report (on/off state)
- Power outage alert

| Field | Type | Description |
|---|---|---|
| `voltage_volt_abs` | float | Mains voltage in V |
| `current_ampere_abs` | float | Load current in A |
| `power_W_abs` | int | Active power in W |
| `energy_kWh_inc` | float | Cumulative energy consumption in kWh |
| `onOff_state_abs` | int | Socket state: 1 = ON (open), 0 = OFF (close) |
| `alarm_state_abs` | int | Power outage alert: 1 = outage active, 0 = power restored |

```javascript
/**
 * TTN Decoder – Milesight WS523 LoRaWAN Smart Portable Socket
 * HSLU adapted version
 *
 * Uplink port: 85 | Byte order: little-endian
 *
 * Field mapping:
 *   voltage_volt_abs    – Mains voltage [V]
 *   current_ampere_abs  – Load current [A]
 *   power_W_abs         – Active power [W]
 *   energy_kWh_inc      – Cumulative energy consumption [kWh]
 *   onOff_state_abs     – Socket state: 1=ON, 0=OFF
 *   alarm_state_abs     – Power outage: 1=outage active, 0=power restored
 */
function decodeUplink(input) {
    var bytes = input.bytes;

    if (bytes.length < 2) {
        return { data: {}, warnings: ["Payload too short"] };
    }

    var decoded = {};

    for (var i = 0; i < bytes.length - 1; ) {
        var channel = bytes[i++];
        var type    = bytes[i++];

        if (channel === 0x03 && type === 0x74) {
            // Voltage: UINT16, unit 0.1 V → V
            decoded.voltage_volt_abs = (bytes[i] | (bytes[i + 1] << 8)) / 10;
            i += 2;

        } else if (channel === 0x04 && type === 0x74) {
            // Current: UINT16, unit mA → A
            decoded.current_ampere_abs = (bytes[i] | (bytes[i + 1] << 8)) / 1000;
            i += 2;

        } else if (channel === 0x05 && type === 0x74) {
            // Active Power: INT16, unit W
            var rawPwr = bytes[i] | (bytes[i + 1] << 8);
            decoded.power_W_abs = (rawPwr & 0x8000) ? rawPwr - 0x10000 : rawPwr;
            i += 2;

        } else if (channel === 0x06 && type === 0x74) {
            // Power Factor: not used – skip
            i += 1;

        } else if (channel === 0x07 && type === 0xC8) {
            // Power Consumption: UINT32, unit Wh → kWh
            var wh = (bytes[i] | (bytes[i+1] << 8) | (bytes[i+2] << 16) | (bytes[i+3] << 24)) >>> 0;
            decoded.energy_kWh_inc = wh / 1000;
            i += 4;

        } else if (channel === 0x08 && type === 0x70) {
            // Socket Status: 0x00=OFF (close), 0x01=ON (open)
            decoded.onOff_state_abs = bytes[i] === 0x00 ? 0 : 1;
            i += 1;

        } else if (channel === 0x09 && type === 0x70) {
            // Power Outage Alert: 0x00=restored, 0x01=outage active
            decoded.alarm_state_abs = bytes[i] === 0x00 ? 0 : 1;
            i += 1;

        } else {
            // Unknown channel/type – skip 1 byte and attempt to resync
            i += 1;
        }
    }

    if (Object.keys(decoded).length === 0) {
        return { data: {}, warnings: ["No known channels found in payload"] };
    }

    return { data: decoded };
}
```
