---
layout: default
title: Dragino - LDS02
parent: Sensors
---

<img src="https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/dragino-lds02_01.jpg" width="250" align="right">

# Dragino - LDS02
{: .no_toc }

- Manufacturer: <a href="https://www.dragino.com/" target="_blank">Dragino</a>
- Product: <a href="https://www.dragino.com/products/lorawan-nb-iot-door-sensor-water-leak/item/181-lds02.html" target="_blank">LDS02</a>

The LDS02 is a LoRaWAN Window/Door sensor.

---

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Specifications
- indoor device
- Price ca. CHF 22.- (03.08.2023)
- Built-in sensors
  - <b>Magnetic reed switch</b>, [open/close]
- Power Supply: 2 common AAA batteries, 1.5 V
  - Expected life time: depending on usage, 16'000 ~ 70'000 state changes
- LoRaWAN version: 1.0.3
- LoRaWAN device class: A
- Size: 69.2 × 29.2 × 15 mm
- Weight: 55 g

---
## Documents
- [Payload description ttn v1.5 (2023-08-03)](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/dragino-lds02_04.txt)
- [Datasheet from dragino.com (2023-08-03)](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/dragino-lds02_02.pdf)
- <a href="http://wiki.dragino.com/xwiki/bin/view/Main/User%20Manual%20for%20LoRaWAN%20End%20Nodes/LDS02%20-%20LoRaWAN%20Door%20Sensor%20User%20Manual/" target="_blank">User Manual (online)</a>

---

## Ordering Info
- Part Number: LDS02-XX
  - XX is frequency band, for ttn in Europe choose EU868
- [Ordering Link](https://www.bastelgarage.ch/lds02-lorawan-door-fenster-sensor-node-868mhz)

---

## Adding the Device to TTN
- The `JoinEUI`, `App EUI` and the `DevEUI` should be on a sticker on the cardboard box.
- Before a device can communicate via "The Things Network" we have to add it to an application.<br>

1. [Create a new application](https://hslu-ige-laes.github.io/lora-devices-ttn/docs/getting_started#create-a-new-application)
2. Under `End devices` in the application click `(+) Register end device`
3. Under `Input method` select `Enter end device specifics manually`
4. Under `Frequency plan` select `Europe 863-870 Mhz (SF9 for RX2 - recommended)`
5. Under `LoRaWAN version` select `1.0.3`
5. Under `JoinEUI` enter the `App EUI` from the App and press `Confirm`
6. Enter as well the `DevEUI` and the `AppKey` from the App
7. Set an end-device name
8. Press `Register end device`
9. Add the payload formatter from below, either to the device itself or if all devices in the app are from the same type, to the application
10. Open the case and take out the batteries
11. Replace the batteries and close the case

- After Configuration, the device restarts automatically and tries to join the network
- Now the device should join the network and you can see the incoming telegrams in the `Live data` section

---

## Important Comissioning Steps
Here are important settings you have to make at comissioning.

Once at first commissioning:
- **Set LoRaWAN Uplink Mode** to **confirmed**, see [Set LoRaWAN Uplink Mode]( #set-lorawan-uplink-mode-type-code-0x05 )
- **Enable Adaptive Data Rate**, see [Set ADR and Data Rate](#set-adr-and-data-rate-type-code-0xa8)

Every time the device gets assigned to a new project:
- **Clear Counter**, see [Clear Counting]( #clear-counting-type-code-0xa6 ) 

---

## Optional Settings

To change settings of the device, you have to send downlink-messages:

1. In the TTN Console on the device view, select the device and change to the tab `Messaging`, select `Downlink`
2. Change the `FPort to 1`
3. Copy/paste the payload from the examples below, e.g. `01 00 0E 10` into the `Payload` field
4. Press `Send`
5. In the `Data` tab you should now see the scheduled telegram. The wisely sensor only receives downlink data after a transmission. Therefore wait max 1 day or manually open/close the window to trigger a new transmission.

### Change Transmit Time Interval / Keep Alive Interval (type code 0x01)
By default, the sensor sends a status message every day. This interval can be reduced to e.g. 1 hour (3600s -> HEX E10).  
`01 00 0E 10` = 3600 seconds.
`01 01 51 80` = 3600 seconds.

### Reset LDS02 (type code 0x04)
If the payload is `04FF`, it will reset the LDS02.

### Set LoRaWAN Uplink Mode (type code 0x05)
- `05 00`: Set uplink to LoRaWAN **unconfirmed** mode  
- `05 01`: Set uplink to LoRaWAN **confirmed** mode

### Clear Counting (type code 0xA6)
Example: `A601` – Clears both the count number and time for the LDS02.

### Enable/Disable Alarm (type code 0xA7)
- `A701`: Enable
- `A700`: Disable

### Set ADR and Data Rate (type code 0xA8)
Format: `A8 aa bb`  
- **aa**: `1` – Enable ADR (Adaptive Data Rate); `0` – Disable ADR 
- **bb**: Set a fixed DR (only valid after `ADR=0`)  

Examples:
- `A8 00 02` – Deactivate ADR and set it DR1 (SF10)
- `A8 01 00` – Enable ADR

---

## Payload Decoder

```javascript
function mapBatteryVoltageAbs(voltage) {
  if (voltage < 2.45) {
    return 0; // Critical, needs replacement
  } else if (voltage < 2.60) {
    return 1; // Warning, running low
  } else if (voltage < 2.90) {
    return 2; // Good
  } else {
    return 3; // Very Good
  }
}

function decodeUplink(input) {
    const bytes = input.bytes;
    const batteryVoltage = ((bytes[0] << 8 | bytes[1]) & 0x3FFF) / 1000;
    const doorStatus = (bytes[0] & 0x80) ? 1 : 0; // 1: open, 0: close
    const waterLeakStatus = (bytes[0] & 0x40) ? 1 : 0;
    const mode = bytes[2];
    const alarm = bytes[9] & 0x01;
  
    let data = {
        battery_volt_abs: batteryVoltage,
        battery_state_abs: mapBatteryVoltageAbs(batteryVoltage)
    };

    switch (input.fPort) {
        case 10:
            const times = bytes[3] << 16 | bytes[4] << 8 | bytes[5];
            const duration = bytes[6] << 16 | bytes[7] << 8 | bytes[8]; // units: min

            switch (mode) {
                case 1: 
                    data.open_state_abs = doorStatus;
                    data.counter_impulse_inc = times;
                    data.openDuration_min_abs = duration;
                    data.alarm_state_abs = alarm;
                    break;
                case 2: 
                    data.waterleak_state_abs = waterLeakStatus;
                    data.counter_impulse_inc = times;
                    data.waterleakDuration_min = duration;
                    data.alarm_state_abs = alarm;
                    break;
                case 3: 
                    data.waterleak_state_abs = doorStatus || waterLeakStatus;
                    data.alarm_state_abs = alarm;
                    break;
                default:
                    break;
            }
            break;
        default:
            return { errors: ["unknown FPort"] };
    }

    return { data: data };
}

```