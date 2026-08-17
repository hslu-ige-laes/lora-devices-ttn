---
layout: default
title: Milesight - WS523
parent: Sensors
---

<img src="https://hslu-ige-laes.github.io/lora-devices-ttn/docs/sensors/milesight-ws523_01.png" width="250" align="right" class="inline"/>

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
 * Payload Decoder
 *
 * Copyright 2025 Milesight IoT
 *
 * @product WS52x
 */
var RAW_VALUE = 0x00;

/* eslint no-redeclare: "off" */
/* eslint-disable */
// Chirpstack v4
function decodeUplink(input) {
    var decoded = milesightDeviceDecode(input.bytes);
    return { data: decoded };
}

// Chirpstack v3
function Decode(fPort, bytes) {
    return milesightDeviceDecode(bytes);
}

// The Things Network
function Decoder(bytes, port) {
    return milesightDeviceDecode(bytes);
}
/* eslint-enable */

function milesightDeviceDecode(bytes) {
    var decoded = {};

    for (var i = 0; i < bytes.length;) {
        var channel_id = bytes[i++];
        var channel_type = bytes[i++];

        // IPSO VERSION
        if (channel_id === 0xff && channel_type === 0x01) {
            decoded.ipso_version = readProtocolVersion(bytes[i]);
            i += 1;
        }
        // HARDWARE VERSION
        else if (channel_id === 0xff && channel_type === 0x09) {
            decoded.hardware_version = readHardwareVersion(bytes.slice(i, i + 2));
            i += 2;
        }
        // FIRMWARE VERSION
        else if (channel_id === 0xff && channel_type === 0x0a) {
            decoded.firmware_version = readFirmwareVersion(bytes.slice(i, i + 2));
            i += 2;
        }
        // TSL VERSION
        else if (channel_id === 0xff && channel_type === 0xff) {
            decoded.tsl_version = readTslVersion(bytes.slice(i, i + 2));
            i += 2;
        }
        // SERIAL NUMBER
        else if (channel_id === 0xff && channel_type === 0x16) {
            decoded.sn = readSerialNumber(bytes.slice(i, i + 8));
            i += 8;
        }
        // LORAWAN CLASS TYPE
        else if (channel_id === 0xff && channel_type === 0x0f) {
            decoded.lorawan_class = readLoRaWANClass(bytes[i]);
            i += 1;
        }
        // RESET EVENT
        else if (channel_id === 0xff && channel_type === 0xfe) {
            decoded.reset_event = readResetEvent(1);
            i += 1;
        }
        // DEVICE STATUS
        else if (channel_id === 0xff && channel_type === 0x0b) {
            decoded.device_status = readDeviceStatus(1);
            i += 1;
        }
        // VOLTAGE
        else if (channel_id === 0x03 && channel_type === 0x74) {
            decoded.voltage = readUInt16LE(bytes.slice(i, i + 2)) / 10;
            i += 2;
        }
        // ACTIVE POWER
        else if (channel_id === 0x04 && channel_type === 0x80) {
            decoded.active_power = readUInt32LE(bytes.slice(i, i + 4));
            i += 4;
        }
        // POWER FACTOR
        else if (channel_id === 0x05 && channel_type === 0x81) {
            decoded.power_factor = readUInt8(bytes[i]);
            i += 1;
        }
        // POWER CONSUMPTION
        else if (channel_id === 0x06 && channel_type == 0x83) {
            decoded.power_consumption = readUInt32LE(bytes.slice(i, i + 4));
            i += 4;
        }
        // CURRENT
        else if (channel_id === 0x07 && channel_type == 0xc9) {
            decoded.current = readUInt16LE(bytes.slice(i, i + 2));
            i += 2;
        }
        // SOCKET STATUS
        else if (channel_id === 0x08 && channel_type == 0x70) {
            var data = bytes[i++];
            decoded.socket_status = readSocketStatus(data & 0x01);
        }
        // DOWNLINK RESPONSE
        else if (channel_id === 0xfe || channel_id === 0xff) {
            var result = handle_downlink_response(channel_type, bytes, i);
            decoded = Object.assign(decoded, result.data);
            i = result.offset;
        } else {
            break;
        }
    }
    return decoded;
}

function handle_downlink_response(channel_type, bytes, offset) {
    var decoded = {};

    switch (channel_type) {
        case 0x03:
            decoded.report_interval = readUInt16LE(bytes.slice(offset, offset + 2));
            offset += 2;
            break;
        case 0x10:
            decoded.reboot = readYesNoStatus(1);
            offset += 1;
            break;
        case 0x28:
            decoded.report_status = readYesNoStatus(1);
            offset += 1;
            break;
        case 0x22:
            // skip first byte
            decoded.delay_time = readUInt16LE(bytes.slice(offset + 1, offset + 3));
            decoded.socket_status = readOnOffStatus(bytes[offset + 3] & 0x0F);
            offset += 4;
            break;
        case 0x23:
            decoded.cancel_delay_task = readUInt8(bytes[offset]);
            // skip next byte
            offset += 2;
            break;
        case 0x24:
            decoded.current_alarm_config = {};
            decoded.current_alarm_config.enable = readEnableStatus(bytes[offset]);
            decoded.current_alarm_config.threshold = readUInt8(bytes[offset + 1]);
            offset += 2;
            break;
        case 0x25:
            var child_lock_data = readUInt16LE(bytes.slice(offset, offset + 2));
            decoded.child_lock_config = {};
            decoded.child_lock_config.enable = readEnableStatus((child_lock_data >>> 15) & 0x01);
            decoded.child_lock_config.lock_time = child_lock_data & 0x7fff;
            offset += 2;
            break;
        case 0x26:
            decoded.power_consumption_enable = readEnableStatus(bytes[offset]);
            offset += 1;
            break;
        case 0x27:
            decoded.reset_power_consumption = readYesNoStatus(1);
            offset += 1;
            break;
        case 0x2c:
            decoded.report_attribute = readYesNoStatus(1);
            offset += 1;
            break;
        case 0x2f:
            decoded.led_indicator_enable = readEnableStatus(bytes[offset]);
            offset += 1;
            break;
        case 0x30:
            decoded.over_current_protection = {};
            decoded.over_current_protection.enable = readEnableStatus(bytes[offset]);
            decoded.over_current_protection.trip_current = readUInt8(bytes[offset + 1]);
            offset += 2;
            break;
        default:
            throw new Error("unknown downlink response");
    }

    return { data: decoded, offset: offset };
}

function readProtocolVersion(bytes) {
    var major = (bytes & 0xf0) >> 4;
    var minor = bytes & 0x0f;
    return "v" + major + "." + minor;
}

function readHardwareVersion(bytes) {
    var major = (bytes[0] & 0xff).toString(16);
    var minor = (bytes[1] & 0xff) >> 4;
    return "v" + major + "." + minor;
}

function readFirmwareVersion(bytes) {
    var major = (bytes[0] & 0xff).toString(16);
    var minor = (bytes[1] & 0xff).toString(16);
    return "v" + major + "." + minor;
}

function readTslVersion(bytes) {
    var major = bytes[0] & 0xff;
    var minor = bytes[1] & 0xff;
    return "v" + major + "." + minor;
}

function readSerialNumber(bytes) {
    var temp = [];
    for (var idx = 0; idx < bytes.length; idx++) {
        temp.push(("0" + (bytes[idx] & 0xff).toString(16)).slice(-2));
    }
    return temp.join("");
}

function readLoRaWANClass(type) {
    var class_map = {
        0: "Class A",
        1: "Class B",
        2: "Class C",
        3: "Class CtoB",
    };
    return getValue(class_map, type);
}

function readResetEvent(status) {
    var status_map = { 0: "normal", 1: "reset" };
    return getValue(status_map, status);
}

function readDeviceStatus(status) {
    var status_map = { 0: "off", 1: "on" };
    return getValue(status_map, status);
}

function readSocketStatus(status) {
    var on_off_map = { 0: "off", 1: "on" };
    return getValue(on_off_map, status);
}

function readEnableStatus(status) {
    var status_map = { 0: "disable", 1: "enable" };
    return getValue(status_map, status);
}

function readYesNoStatus(status) {
    var yes_no_map = { 0: "no", 1: "yes" };
    return getValue(yes_no_map, status);
}

function readOnOffStatus(status) {
    var on_off_map = { 0: "off", 1: "on" };
    return getValue(on_off_map, status);
}

/* eslint-disable */
function readUInt8(bytes) {
    return bytes & 0xff;
}

function readInt8(bytes) {
    var ref = readUInt8(bytes);
    return ref > 0x7f ? ref - 0x100 : ref;
}

function readUInt16LE(bytes) {
    var value = (bytes[1] << 8) + bytes[0];
    return value & 0xffff;
}

function readUInt32LE(bytes) {
    var value = (bytes[3] << 24) + (bytes[2] << 16) + (bytes[1] << 8) + bytes[0];
    return (value & 0xffffffff) >>> 0;
}

function getValue(map, key) {
    if (RAW_VALUE) return key;

    var value = map[key];
    if (!value) value = "unknown";
    return value;
}

//if (!Object.assign) {
    Object.defineProperty(Object, "assign", {
        enumerable: false,
        configurable: true,
        writable: true,
        value: function (target) {
            "use strict";
            if (target == null) {
                throw new TypeError("Cannot convert first argument to object");
            }

            var to = Object(target);
            for (var i = 1; i < arguments.length; i++) {
                var nextSource = arguments[i];
                if (nextSource == null) {
                    continue;
                }
                nextSource = Object(nextSource);

                var keysArray = Object.keys(Object(nextSource));
                for (var nextIndex = 0, len = keysArray.length; nextIndex < len; nextIndex++) {
                    var nextKey = keysArray[nextIndex];
                    var desc = Object.getOwnPropertyDescriptor(nextSource, nextKey);
                    if (desc !== undefined && desc.enumerable) {
                        // concat array
                        if (Array.isArray(to[nextKey]) && Array.isArray(nextSource[nextKey])) {
                            to[nextKey] = to[nextKey].concat(nextSource[nextKey]);
                        } else {
                            to[nextKey] = nextSource[nextKey];
                        }
                    }
                }
            }
            return to;
        },
    });
//}
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
 * HSLU adapted version – based on official Milesight decoder
 *
 * Copyright 2025 Milesight IoT (structure), HSLU adaptations (field names & units)
 *
 * Uplink port: 85 | Byte order: little-endian
 *
 * Field mapping:
 *   voltage_volt_abs    – Mains voltage [V]
 *   current_ampere_abs  – Load current [A]  (mA ÷ 1000)
 *   power_W_abs         – Active power [W]
 *   energy_kWh_inc      – Cumulative energy [kWh]  (Wh ÷ 1000)
 *   onOff_state_abs     – Socket state: 1=ON, 0=OFF
 *   alarm_state_abs     – Power outage: 1=outage active, 0=power restored
 *
 * Example payload:  08700105816307C90C0103748F0906832B4C0400048041000000
 * Expected result:
 *   onOff_state_abs     = 1         (socket ON)
 *   voltage_volt_abs    = 244.7     (V)
 *   current_ampere_abs  = 0.268     (A)
 *   power_W_abs         = 65        (W)
 *   energy_kWh_inc      = 281.643   (kWh)
 */
function decodeUplink(input) {
    var decoded = milesightDeviceDecode(input.bytes);
    return { data: decoded };
}

function milesightDeviceDecode(bytes) {
    var decoded = {};

    for (var i = 0; i < bytes.length;) {
        var channel_id   = bytes[i++];
        var channel_type = bytes[i++];

        // IPSO / hardware / firmware / serial info (join message)
        if (channel_id === 0xff && channel_type === 0x01) {
            i += 1; // ipso version – skip
        } else if (channel_id === 0xff && channel_type === 0x09) {
            i += 2; // hardware version – skip
        } else if (channel_id === 0xff && channel_type === 0x0a) {
            i += 2; // firmware version – skip
        } else if (channel_id === 0xff && channel_type === 0xff) {
            i += 2; // tsl version – skip
        } else if (channel_id === 0xff && channel_type === 0x16) {
            i += 8; // serial number – skip
        } else if (channel_id === 0xff && channel_type === 0x0f) {
            i += 1; // lorawan class – skip
        } else if (channel_id === 0xff && channel_type === 0xfe) {
            i += 1; // reset event – skip
        } else if (channel_id === 0xff && channel_type === 0x0b) {
            i += 1; // device status – skip

        // VOLTAGE  ch=0x03, type=0x74, UINT16, unit 0.1 V → V
        } else if (channel_id === 0x03 && channel_type === 0x74) {
            decoded.voltage_volt_abs = readUInt16LE(bytes.slice(i, i + 2)) / 10;
            i += 2;

        // ACTIVE POWER  ch=0x04, type=0x80, UINT32, unit W
        } else if (channel_id === 0x04 && channel_type === 0x80) {
            decoded.power_W_abs = readUInt32LE(bytes.slice(i, i + 4));
            i += 4;

        // POWER FACTOR  ch=0x05, type=0x81, UINT8, unit % – not used
        } else if (channel_id === 0x05 && channel_type === 0x81) {
            i += 1;

        // POWER CONSUMPTION  ch=0x06, type=0x83, UINT32, unit Wh → kWh
        } else if (channel_id === 0x06 && channel_type === 0x83) {
            decoded.energy_kWh_inc = readUInt32LE(bytes.slice(i, i + 4)) / 1000;
            i += 4;

        // CURRENT  ch=0x07, type=0xC9, UINT16, unit mA → A
        } else if (channel_id === 0x07 && channel_type === 0xc9) {
            decoded.current_ampere_abs = readUInt16LE(bytes.slice(i, i + 2)) / 1000;
            i += 2;

        // SOCKET STATUS  ch=0x08, type=0x70, bit0: 0=off, 1=on
        } else if (channel_id === 0x08 && channel_type === 0x70) {
            decoded.onOff_state_abs = (bytes[i] & 0x01) === 0x01 ? 1 : 0;
            i += 1;

        // POWER OUTAGE ALERT  ch=0x09, type=0x70, 0x00=restored, 0x01=outage
        } else if (channel_id === 0x09 && channel_type === 0x70) {
            decoded.alarm_state_abs = bytes[i] === 0x00 ? 0 : 1;
            i += 1;

        // POWER OUTAGE ALERT (device-level)  ch=0xFF, type=0x3F
        // observed in field: ff3fff = outage active, ff3f00 = power restored
        } else if (channel_id === 0xff && channel_type === 0x3f) {
            decoded.alarm_state_abs = bytes[i] === 0x00 ? 0 : 1;
            i += 1;

        } else {
            break; // unknown channel – stop parsing
        }
    }

    // If any power measurement is present, check voltage to determine alarm state:
    // voltage == 0 → mains power lost → alarm_state_abs = 1
    // voltage  > 0 → mains power ok  → alarm_state_abs = 0
    // Only overwrite if no explicit outage alert was already set in this telegram
    if ((decoded.voltage_volt_abs !== undefined ||
         decoded.current_ampere_abs !== undefined ||
         decoded.power_W_abs !== undefined ||
         decoded.energy_kWh_inc !== undefined) &&
        decoded.alarm_state_abs === undefined) {
        decoded.alarm_state_abs = (decoded.voltage_volt_abs === 0) ? 1 : 0;
    }

    return decoded;
}

// --- helpers (little-endian) ---

function readUInt8(byte) {
    return byte & 0xff;
}

function readUInt16LE(bytes) {
    return ((bytes[1] << 8) + bytes[0]) & 0xffff;
}

function readUInt32LE(bytes) {
    return ((bytes[3] << 24) + (bytes[2] << 16) + (bytes[1] << 8) + bytes[0]) >>> 0;
}
```
