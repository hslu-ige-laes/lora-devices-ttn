---
layout: default
title: Plenom - Kuando-Busylight
parent: Sensors
---

<img src="https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/plenom-kuando-busylight_01.png" width="250" align="right">

# Plenom - Kuando-Busylight
{: .no_toc }

- Manufacturer: <a href="https://www.plenom.com/" target="_blank">Plenom</a>
- Product: <a href="https://www.plenom.com/products/kuando-busylight-iot-lorawan/" target="_blank">Kuando-Busylight</a>

The Kuando-Busylight is an LED which can get changed through  LoRaWAN downlink messages.
The Busylight can show any color and be used as a visual notifier, indicating a certain event, status or as a cue for specific handling.

---

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Specifications
- indoor device
- Price ca. CHF 75.- (20.08.2025)
- Multi color RGB LED light
  - Color, brightness and flashing can be customized through LoRaWAN payload
  - 360° visibility
- LoRaWAN Class C Device
- LoRaWAN version 1.0.3
- Power Supply: over USB with 3m cable, adapter included
- Size: 38 × 38 mm

---
## Documents
- [Datasheet pdf (2025-08-20)](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/plenom-kuando-busylight_03.pdf)
- [Getting Started pdf (2025-08-20)](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/plenom-kuando-busylight_02.pdf)

---

## Ordering Info
- Part Number: kuando Busylight IoT Omega - LoRaWAN - EU
- [Ordering Link](https://shop.busylight.com/cart.php?suggest=04ed7466-5022-4844-b43e-fdcb6c418513)

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
7. Additional LoRaWAN class capabilities: `Class C`
8. Set an end-device name
9. Press `Register end device`
10. Add the payload formatter from below, either to the device itself or if all devices in the app are from the same type, to the application
11. [Switch on the device](https://hslu-ige-laes.github.io/lora-devices-ttn/docs/seeedstudio-sensecap-s2103#led-states)

- Now the device should join the network and you can see the incoming telegrams in the `Live data` section

---

## Optional Settings

### Change sampling interval
To change the sampling interval, you have to send the device configuration telegrams (Downlink-Messages)
The time interval in minutes at which the sensor queries the current values.

1. In the TTN Console on the device view, select the device and change to the tab `Messaging`, select `Downlink`
2. Change the `FPort to 15`
3. Copy/paste the payload, e.g. `01000258` into the `Payload` field to set the led to red
4. Press `Send`
5. In the `Data` tab you should now see the scheduled telegram. The device receives downlink data immediately, because its a class C device.

#### Example messages

- solid white: `FF FF FF FF 00`
- solid red: `FF 00 00 FF 00`
- solid green: `00 FF 00 FF 00`
- red fast blink: `FF 00 00 20 20`
- red slow blink: `FF 00 00 A0 A0`
- green fast blink: `00 FF 00 20 20`
- green slow blink: `00 FF 00 A0 A0`
- off: `00 00 00 00 00`

---

## Payload Decoder
### Uplink

```javascript
function decodeUplink(input) {
  if (input.bytes.length === 24) {
    return {
      data: {
        "counter_inc@received": byteArrayToLong(input.bytes, 8),
        "counter_inc@sent": byteArrayToLong(input.bytes, 12),
        "last_color@red": input.bytes[16],
        "last_color@blue": input.bytes[17],
        "last_color@green": input.bytes[18],
        "last_color@ontime": input.bytes[19],
        "last_color@offtime": input.bytes[20],
        "adr_state_abs": input.bytes[23],
      },
      warnings: [],
      errors: [],
    };
  }
}

function byteArrayToLong(byteArray, from) {
  return (
    byteArray[from] |
    (byteArray[from + 1] << 8) |
    (byteArray[from + 2] << 16) |
    (byteArray[from + 3] << 24)
  );
}
```

### Downlink

```javascript
function encodeDownlink(input) {
  return {
    bytes: [
      input.data.red & 0xFF,
      input.data.blue & 0xFF,
      input.data.green & 0xFF,
      input.data.ontime & 0xFF,
      input.data.offtime & 0xFF
    ],
    fPort: 15,
    warnings: [],
    errors: []
  };
}

function decodeDownlink(input) {
  return {
    data: {
      red: input.bytes[0],
      green: input.bytes[2],
      blue: input.bytes[1],
      ontime: input.bytes[3],
      offtime: input.bytes[4]
    },
    warnings: [],
    errors: []
  };
}
```
