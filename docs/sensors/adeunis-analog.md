---
layout: default
title: Adeunis - Analog
parent: Sensors
---

<img src="https://hslu-ige-laes.github.io/lora-devices-ttn/docs/sensors/adeunis-analog_01.png" width="250" align="right" class="inline"/>

# Adeunis - Analog
{: .no_toc }

- Manufacturer: <a href="https://www.adeunis.com/" target="_blank">Adeunis</a>
- Product: <a href="https://www.adeunis.com/en/produit/analog-wired-sensor-interface/" target="_blank">Analog</a>

The Adeunis Analog is a ready-to-use IoT sensor that transforms any type of 0-10 V or 4-20 mA wired sensor into a communicating sensor. It has 2 independently configurable analog inputs (channel A and channel B).

### Current Sensor Variant

<img src="https://hslu-ige-laes.github.io/lora-devices-ttn/docs/sensors/adeunis-analog_03.jpg" width="150" align="right" class="inline"/>

Adeunis bietet den Analog-Transmitter auch als **Current Sensor** Bundle an, inkl. Stromwandler (CT Clamp). Dabei wird der 4-20mA Eingang des Analog-Transmitters genutzt. Verfügbare Varianten:


| Part Number | Stromwandler |
|---|---|
| ARF8190BA-B01 | 50A |
| ARF8190BA-B02 | 100A |
| ARF8190BA-B03 | 500A |

- Precision: ±2%
- Cable length: ca. 70 cm
- Operating Temperature Transducer: -25 ... +60 °C
- <a href="https://www.adeunis.com/en/produit/current-sensor-current-measurement/" target="_blank">Current Sensor Product page</a>

---

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Specifications
- indoor/outdoor device
- 2 analog inputs independently configurable as 0-10V or 4-20mA
- 2 digital inputs associated with analog inputs
- Protection: [IP67](https://en.wikipedia.org/wiki/IP_Code)
- Power Supply:
  - **ANALOG** (ARF8190BA): Replaceable battery, 2600 mAh
  - **ANALOG PWR** (ARF8200AA): External power supply, 6-24V DC, max. 90 mA
- Size: 105 x 50 x 27 mm
- Weight: 70 g (battery version), 49 g (powered version)
- Operating Temperature Range:
  - Battery version: -25 ... +70 °C
  - Powered version: -25 ... +40 °C
- Humidity: 0 ... 85 %rH (non-condensing)
- Minimum scan period: 30 seconds
- Data logging: up to 24 samples per frame
- Mounting: DIN Rail, Tube, Wall, Collar

---

## Documents
  - [Datasheet Analog LoRaWAN EU863-870](https://hslu-ige-laes.github.io/lora-devices-ttn/docs/sensors/adeunis-analog_02.pdf)
  - [Datasheet Current Sensor LoRaWAN EU863-870](https://hslu-ige-laes.github.io/lora-devices-ttn/docs/sensors/adeunis-analog_04.pdf)

---

## Ordering Info
- Part Number: ARF8190BA (battery) / ARF8200AA (external power)
- [Product page](https://www.adeunis.com/en/produit/analog-wired-sensor-interface/)

---

## Device specific Information

### Operating Modes

The device has the following operating modes:

```
                        ┌───────────┐
                        │ PARK MODE │ (standby, minimal consumption)
                        └─────┬─────┘
                              │ Magnet on product > 5s
                              ▼
  ┌──────────────┐     ┌──────────────┐         ┌─────────────────────┐
  │ COMMAND MODE │◄────│  PRODUCTION  │◄────────│ Replacing the       │
  │              │────►│     MODE     │         │ battery             │
  └──────────────┘     └──────┬───────┘         └─────────────────────┘
  USB-C connect/               │                          ▲
  disconnect            Send "battery                     │
                         low" flag                        │
                              │                           │
                              ▼                           │
                        ┌───────────┐                     │
                        │    OFF    │─────────────────────┘
                        │(dead bat.)│
                        └───────────┘
```

- **PARK mode**: The product is delivered in PARK mode. It is in standby and its consumption is minimal. To switch out of PARK mode, hold a magnet on top of the product for more than 5 seconds. The green LED illuminates to indicate magnet detection and then flashes quickly during the starting phase. The device then sends its configuration and data frames.
- **PRODUCTION mode**: Normal operating mode for final use, optimized for maximum battery autonomy.
- **COMMAND mode**: Allows configuration of the product registers. Connect a USB-C cable and use the [IoT Configurator](https://www.adeunis.com/en/produit/analog-wired-sensor-interface/). Requires the [Silabs USB to UART Bridge VCP Driver](https://www.silabs.com/developers/usb-to-uart-bridge-vcp-drivers). To exit, use the disconnect function in the IoT Configurator or disconnect the USB-C cable. The product returns to its previous mode (PARK or PRODUCTION).
- **OFF**: The product switches off automatically when the battery is dead. Replace the battery and it returns to PRODUCTION mode.

**Note**<br>
A deactivation via magnet is not described in the manufacturer documentation.

### LED States

| Mode | LED red | LED green |
|---|---|---|
| PARK mode | OFF | OFF |
| Magnet detection (1 to 6 sec) | OFF | ON from detection of the magnet, max. 1 sec |
| Product start (after magnet detection) | OFF | Rapid flashing, 6 cycles (100 ms ON / 100 ms OFF) |
| Joining process | Flashing: 50 ms ON / 1 sec OFF | Flashing: 50 ms ON / 1 sec OFF (just after red LED) |
| JOIN accept | Flashing: 50 ms ON / 50 ms OFF (6×) | Flashing: 50 ms ON / 50 ms OFF (just before red LED) |
| Network quality test running | 10 to 20 sec ON | 10 to 20 sec ON |
| Network quality test: bad coverage | 10 sec ON | OFF |
| Network quality test: medium coverage | 10 sec ON | 10 sec ON |
| Network quality test: good coverage | OFF | 10 sec ON |
| Magnet detection in PRODUCTION mode | OFF | Flashing 50 ms ON / 50 ms OFF after 3 sec of magnet presence |
| Low battery level | Flashing 500 ms ON every 60 sec | OFF |
| Switching to command mode (USB) | ON | ON |
| Product faulty (return to factory) | ON | OFF |

### Configuration
- The device can be configured via USB using the Adeunis IoT Configurator software or via LoRaWAN downlinks.
- Each of the 2 channels (A and B) can be independently configured as:
  - **0-10V** voltage input
  - **4-20mA** current input
  - **deactivated**
- Threshold alarms can be configured for each channel (low, high or both).
- Transmission can be periodic and/or event-driven.

---

## Adding the Device to TTN
- Before a device can communicate via "The Things Network" we have to add it to an application.<br>

1. [Create a new application](https://hslu-ige-laes.github.io/lora-devices-ttn/docs/getting_started#create-a-new-application)
2. Under `End devices` in the application click `(+) Register end device`
3. Under `Input method` select `Select the end device in the LoRaWAN Device Repository`
4. Enter the following device information
   - `End device brand` select `Adeunis`
   - `Model` select `Analog`
   - `Hardware Ver.` select the appropriate version
   - `Firmware` select the appropriate version
   - `Profile (Region)` select `EU_863_870`
5. Under `Frequency plan` select `Europe 863-870 Mhz (SF9 for RX2 - recommended)`
6. As `JoinEUI` enter the `App EUI`, fill in as well the `DevEUI` and the `AppKey`
7. Set an end-device name
8. Press `Register end device`

- Now the device should join the network and you can see the incoming telegrams in the `Live data` section
- The payload formatter should already be preset. If not, you can copy/paste it from below

---

## Optional Settings

The Adeunis Analog can be configured either via **USB** (using the Adeunis IoT Configurator software) or via **LoRaWAN downlink messages**.

For detailed register descriptions refer to the [Technical Reference Manual](https://adeunis.notion.site/Technical-Reference-Manual-ANALOG-V2-ANALOG-PWR-V2-LoRaWAN-Sigfox-4f174179b13f46db9dcb027b8f913772).

### Downlink mechanism

All configuration downlinks are sent on **FPort 1**. The Adeunis platform uses a register-based system with two downlink frame types:

| Downlink Frame | Description | Response (Uplink) |
|---|---|---|
| `0x40` | Read register(s) | `0x31` with register values |
| `0x41` | Set register(s) | `0x33` with status (success/failure) |

Registers are addressed via a **CONFID** index: `Register number = 300 + CONFID`. For example, CONFID `0x00` = Register S300, CONFID `0x14` = Register S320.

### Key registers

| Register | CONFID (hex) | Description | Unit | Default |
|---|---|---|---|---|
| S300 | `0x00` | Keep alive period | × 10 min | 6 (= 60 min) |
| S301 | `0x01` | Periodic data transmission period | × 10 min | 1 (= 10 min, minimum) |

### Change keep alive period

1. In the TTN Console on the device view, select the device and change to the tab `Messaging`, select `Downlink`
2. Change the `FPort to 1`
3. Copy/paste the payload into the `Payload` field
4. Press `Send`
5. The device only receives downlink data after a transmission. Wait for the next uplink or trigger one manually.

#### Examples

- Keep alive every 60 min (default): `41 00 06`
- Keep alive every 120 min: `41 00 0C`
- Keep alive every 360 min (6h): `41 00 24`
- Keep alive every 1440 min (24h): `41 00 90`

### Change data transmission period

Same procedure as above, but with register S301 (CONFID `0x01`).

**Note:** The minimum data transmission period is **10 minutes** (value = 1). Shorter intervals like 5 minutes are not possible in normal operating mode.

#### Examples

- Data every 10 min (default, minimum): `41 01 01`
- Data every 30 min: `41 01 03`
- Data every 60 min: `41 01 06`

### Read current configuration

To read the current values of the keep alive and data transmission period, send the following downlink on FPort 1:

`40 00 01`

The device will respond with an uplink frame `0x31` containing the current values of registers S300 and S301.

**Note**<br>
After setting registers via `0x41`, the device saves the configuration and performs a restart automatically. For a complete list of all configurable registers (thresholds, channel types, debounce, etc.) refer to the [Technical Reference Manual](https://adeunis.notion.site/Technical-Reference-Manual-ANALOG-V2-ANALOG-PWR-V2-LoRaWAN-Sigfox-4f174179b13f46db9dcb027b8f913772).

---

## Payload formatter

```javascript
function decodeUplink(input) {
  var data = {};
  var bytes = input.bytes;

  // Status byte
  data.battery_state_abs = (bytes[1] & 0x02) ? 0 : 3; // lowBattery flag: 0=critical, 3=good

  var frameCode = bytes[0];

  if (frameCode === 0x42 || frameCode === 0x30) {
    // 0x42 Analog data / 0x30 Analog keep alive
    var typeA = bytes[2] & 0x0f;
    var rawValueA = ((bytes[2] & 0x00) << 24 | (bytes[3]) << 16 | (bytes[4]) << 8 | bytes[5]) & 0x00ffffff;

    if (typeA === 1) {
      data["voltage_volt_abs@channelA"] = parseFloat((rawValueA / 1000000).toFixed(3));
    } else if (typeA === 2) {
      data["current_ampere_abs@channelA"] = parseFloat((rawValueA / 100000000).toFixed(6));
    }

    var typeB = bytes[6] & 0x0f;
    var rawValueB = ((bytes[6] & 0x00) << 24 | (bytes[7]) << 16 | (bytes[8]) << 8 | bytes[9]) & 0x00ffffff;

    if (typeB === 1) {
      data["voltage_volt_abs@channelB"] = parseFloat((rawValueB / 1000000).toFixed(3));
    } else if (typeB === 2) {
      data["current_ampere_abs@channelB"] = parseFloat((rawValueB / 100000000).toFixed(6));
    }
  }

  return {
    data: data,
    warnings: [],
    errors: [],
  };
}
```
