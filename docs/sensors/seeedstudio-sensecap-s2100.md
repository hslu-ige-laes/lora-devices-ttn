---
layout: default
title: Seeedstudio - SenseCap-S2100
parent: Sensors
---

<img src="https://hslu-ige-laes.github.io/lora-devices-ttn/docs/sensors/seeedstudio-sensecap-s2100_01.jpg" width="250" align="right">

# Seeedstudio - SenseCap-S2100
{: .no_toc }

- Manufacturer: <a href="https://www.seeedstudio.com/" target="_blank">Seeedstudio</a>
- Product: <a href="https://www.seeedstudio.com/SenseCAP-S2100-LoRaWAN-Data-Logger-p-5361.html" target="_blank">SenseCap S2100</a>

The SenseCap S2100 is a LoRaWAN indoor/outdoor Data Logger for RS485-Modbus/Analog/GPIO devices/sensors

---

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Specifications

- indoor/outdoor device
- Price ca. CHF 85.- (22.08.2023)
- Sensors/Devices/Interfaces
  - **RS485 Modbus-RTU**, 1 x A, 1 x B, 1 x GND, 
  - **Analogue Input**, 0 to 10V and 4 to 20mA, 1 or 2 x signal pin
  - **GPIO**, Level and Pulse Count
  - **Switchable 5V and 3V output for feeding the connected sensor**
  - Each Data logger can only be connected to one type of sensor
  - In Modbus mode...
    - it is only possible to fetch data from a single device.
    - the device operates exclusively as a master; functioning as a slave and listening for incoming telegrams is not supported.
  - Expected life time: depending on usage, 5 ... 10 years
- Battery Li-SOCl2, ER34615, 19Ah, 3.6V
- LoRaWAN version: 1.0.3
- LoRaWAN device class: A
- Protection: [IP66](https://en.wikipedia.org/wiki/IP_Code)
- Operating Temperature: -60 ... +80 °C
- Size: 165.6 × 63 × 63.7 mm
- Weight: 450 g

---

## Documents/Links
- [S2100 Sensor User Guide from seeedstudio.com (2023-08-09)](https://hslu-ige-laes.github.io/lora-devices-ttn/docs/sensors/seeedstudio-sensecap-s2100_03.pdf)

---

## Ordering Info
- Part Number: 114992872 Model S2100
- [Ordering Link](https://www.bastelgarage.ch/sensecap-s2100-lorawan-data-logger)

---

## Device specific Information

## Button Actions, Modes and LED States

![2-in-1 Configuration Button and LED](https://hslu-ige-laes.github.io/lora-devices-ttn/docs/sensors/seeedstudio-sensecap-s2103_04.png "Power on the SenseCap")

| Action | Description | Green LED Status |
|--------|-------------|------------------|
| First power up, press and hold for 3s      | Power on and activate the Bluetooth           | LED flashes at 1s frequency, waiting for Bluetooth connection. If Bluetooth not connected within 1 minute, the device will shut down again |
| Press once      | Reboot device and join LoRa network           | 1. The LED will be on for 5 seconds for initialization <br>2. Waiting to join LoRa network: breathing light flashing <br>3. Join LoRa network success: LED flashes fast for 2s <br>4. LoRa network join failure: LED suddenly stop.                |
| Press and hold for 3s      | Activate Bluetooth again           | 1. Waiting for Bluetooth connection: LED flashes at 1s frequency <br>2. Enter configuration mode after Bluetooth connection is successful: LED flashes at 2s frequency <br><br>If Bluetooth is not connected within 1 minute, the device will reboot and join Lora network.                |
| Press and hold for 9s      |  Power off           | In the 3rd second LED will start flashing at 1s frequency, release the button when the light is steady on, the light will then turn off.                |

**Note**<br>
After power off, you need to reconfigure the frequency band. Power off is recommended when not deployed.

---

## Adding the Device to TTN
- Configure the device via Bluetooth with the `SenseCAP Mate App`. [See User Guide chapter 5.2 for details](https://hslu-ige-laes.github.io/lora-devices-ttn/docs/sensors/seeedstudio-sensecap-s2103_03.pdf)
  -> you don't have to create an account, simply click `Skip` on top right
- In the Basic settings, select the Platform 'The Things Network' (not SenseCAP for The Things Network!)
- Configure the device to your needs, select Frequency Plan 'EU868'
- Copy the `JoinEUI`, `App EUI` and the `DevEUI` and send it from the smartphone via E-Mail to your computer.
- Before a device can communicate via "The Things Network" we have to add it to an application.<br>

1. [Create a new application](https://hslu-ige-laes.github.io/lora-devices-ttn/docs/getting_started#create-a-new-application)
2. Under `Overview` click `(+) Register device`
3. Under `Input method` select `Select the end device in the LoRaWAN Device Repository`
4. Enter the following device information
   - `End device brand` select `SenseCAP`
   - `Model` select `SenseCAP S2100...`
   - `Hardware Ver.` select `1.0` or whatever is possible or on the sticker
   - `Firmware` select `1.0` or whatever is possible or on the sticker
	 - `Profile (Region)` select `EU_863_870`
5. Under `Frequency plan` select `Europe 863-870 Mhz (SF9 for RX2 - recommended)`
6. Under `JoinEUI` enter the `App EUI` from the App
7. Enter as well the `DevEUI` and the `AppKey` from the sticker
8. Set an end-device name
9. Press `Register end device`
10. [Switch on the device](https://hslu-ige-laes.github.io/lora-devices-ttn/docs/seeedstudio-sensecap-s2103#led-states)

- After Configuration, the device restarts automatically and tries to join the network
- Now the device should join the network and you can see the incoming telegrams in the `Live data` section
- The payload formatter should already be preset. If not, you can copy/paste it from below

---

## Payload formatter

```javascript
/**
 * SenseCAP S2100 Data Logger – generischer TTN Uplink Decoder
 * Funktioniert für jede Konfiguration (RS485, 0–10 V, 4–20 mA, GPIO).
 *
 * Die S2100 überträgt pro Messung nur Nummer (1..15) und Wert, nicht die
 * physikalische Grösse. Der Decoder gibt deshalb jede Messung generisch aus:
 *     "value_abs@channel<Nr>": <Wert>
 * Der Wert ist bereits das Ergebnis der in der App konfigurierten Umrechnung
 * (Y = A·x + B). Die Zuordnung zu einem konkreten Typ (z.B. voltage_volt_abs)
 * erfolgt pro Gerät im Backend.
 *
 * Optional: Einzelne Messungen hier fest einem Typ zuordnen (leer = generisch).
 *   { 1: { type: "voltage_volt_abs", channel: "channel1" } }
 */
const CHANNEL_MAP = {};

// Frame-Längen in Bytes inkl. 1 Byte Data-ID (aus offiziellem SenseCAP-Decoder)
const FRAME_LENGTH = {
    0x01: 11, 0x20: 11, 0x21: 11, 0x30: 11, 0x31: 11, 0x33: 11,
    0x40: 11, 0x41: 11, 0x42: 11, 0x43: 11, 0x44: 11, 0x45: 11,
    0x02: 9,
    0x03: 2, 0x06: 2,
    0x05: 5, 0x34: 5,
    0x04: 10, 0x10: 10, 0x32: 10, 0x35: 10, 0x36: 10,
    0x37: 10, 0x38: 10, 0x39: 10,
};

const SENSOR_ERRORS = {
    0x00: "CCL_SENSOR_ERROR_NONE",
    0x01: "CCL_SENSOR_NOT_FOUND",
    0x02: "CCL_SENSOR_WAKEUP_ERROR",
    0x03: "CCL_SENSOR_NOT_RESPONSE",
    0x04: "CCL_SENSOR_DATA_EMPTY",
    0x05: "CCL_SENSOR_DATA_HEAD_ERROR",
    0x06: "CCL_SENSOR_DATA_CRC_ERROR",
    0x07: "CCL_SENSOR_DATA_B1_NO_VALID",
    0x08: "CCL_SENSOR_DATA_B2_NO_VALID",
    0x09: "CCL_SENSOR_RANDOM_NOT_MATCH",
    0x0A: "CCL_SENSOR_PUBKEY_SIGN_VERIFY_FAILED",
    0x0B: "CCL_SENSOR_DATA_SIGN_VERIFY_FAILED",
    0x0C: "CCL_SENSOR_DATA_VALUE_HI",
    0x0D: "CCL_SENSOR_DATA_VALUE_LOW",
    0x0E: "CCL_SENSOR_DATA_VALUE_MISSED",
    0x0F: "CCL_SENSOR_ARG_INVAILD",
    0x10: "CCL_SENSOR_RS485_MASTER_BUSY",
    0x11: "CCL_SENSOR_RS485_REV_DATA_ERROR",
    0x12: "CCL_SENSOR_RS485_REG_MISSED",
    0x13: "CCL_SENSOR_RS485_FUN_EXE_ERROR",
    0x14: "CCL_SENSOR_RS485_WRITE_STRATEGY_ERROR",
    0x15: "CCL_SENSOR_CONFIG_ERROR",
    0xFF: "CCL_SENSOR_DATA_ERROR_UNKONW",
};

function decodeUplink(input) {
    try {
        const bytes = input.bytes;
        const decoded = {};
        const warnings = [];

        let pos = 0;
        while (pos < bytes.length) {
            const dataId = bytes[pos];
            const len = FRAME_LENGTH[dataId];

            if (len === undefined) {
                warnings.push("Unknown frame id 0x" + toHex(dataId) + " at byte " + pos);
                break; // Länge unbekannt -> Rest nicht interpretierbar
            }
            if (pos + len > bytes.length) {
                warnings.push("Truncated frame 0x" + toHex(dataId));
                break;
            }

            const f = bytes.slice(pos + 1, pos + len); // Frame ohne Data-ID

            switch (dataId) {
                // Messwerte: [Kanal-Byte][1 Byte reserviert][int32 Wert1][int32 Wert2]
                case 0x30:
                case 0x31:
                case 0x33:
                    putChannelValues(decoded, f[0], readInt32BE(f, 2), readInt32BE(f, 6));
                    break;

                // Messwerte: [Kanal-Byte][int32 Wert1][int32 Wert2]
                case 0x32:
                    putChannelValues(decoded, f[0], readInt32BE(f, 1), readInt32BE(f, 5));
                    break;

                // Batterie
                case 0x03:
                    decoded.battery_perc_abs = f[0];
                    break;

                // Geräteinfo: [Batt][HW 2][FW 2][Intervall 2][Threshold-Intervall 2]
                case 0x39:
                case 0x04:
                    decoded.battery_perc_abs = f[0];
                    break;

                // Sensorfehler -> nur als TTN-Warning
                case 0x06:
                    if (f[0] !== 0x00) {
                        warnings.push(SENSOR_ERRORS[f[0]] || "CC_OTHER_FAILED (0x" + toHex(f[0]) + ")");
                    }
                    break;

                // 0x05 (Intervalle), 0x34 (Logger-Konfig), 0x35–0x38 (Threshold-Frames)
                // und alle anderen bekannten IDs: aktuell nicht in der Lookup-Tabelle -> übersprungen
                default:
                    break;
            }

            pos += len;
        }

        const result = { data: decoded };
        if (warnings.length) result.warnings = warnings;
        return result;

    } catch (e) {
        return { data: null, errors: [e.message] };
    }
}

/**
 * Kanal-Byte: oberes Nibble = measurementId von Wert 1, unteres = von Wert 2.
 * Nibble 0 bedeutet: Wert nicht belegt.
 */
function putChannelValues(decoded, channelByte, value1, value2) {
    const id1 = (channelByte >> 4) & 0x0F;
    const id2 = channelByte & 0x0F;
    if (id1) putValue(decoded, id1, value1 / 1000);
    if (id2) putValue(decoded, id2, value2 / 1000);
}

function putValue(decoded, measurementId, value) {
    const map = CHANNEL_MAP[measurementId];
    const channel = (map && map.channel) || ("channel" + measurementId);
    if (map && map.type) {
        decoded[map.type + "@" + channel] = round(value, 3);
    } else {
        decoded["value_abs@" + channel] = round(value, 3);
    }
}

function round(v, digits) {
    return parseFloat(v.toFixed(digits));
}

// Signed 32-bit Big-Endian, liefert immer eine Zahl (kein String)
function readInt32BE(arr, offset) {
    const v = ((arr[offset] << 24) | (arr[offset + 1] << 16) | (arr[offset + 2] << 8) | arr[offset + 3]);
    return v | 0;
}

function toHex(n) {
    return ("0" + (n & 0xFF).toString(16)).slice(-2).toUpperCase();
}
```