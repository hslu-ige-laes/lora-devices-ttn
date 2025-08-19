---
layout: default
title: Dragino - LTC2-FT
parent: Sensors
---

<img src="https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/dragino-ltc2-ft_01.jpg" width="250" align="right">

# Dragino - LTC2-LB
{: .no_toc }

- Manufacturer: <a href="https://www.dragino.com/" target="_blank">Dragino</a>
- Product: <a href="https://www.dragino.com/products/temperature-humidity-sensor/item/170-ltc2.html" target="_blank">LTC2-FT</a>

The LTC2-FT is a LoRaWAN Waterproof Outdoor Temperature Sensor with 2 external PT100 Probes.
<br>
**Attention:** This is an older procuct, the successor is the [LTC2-LB](https://hslu-ige-laes.github.io/lora-devices-ttn/docs/sensors/dragino-ltc2-lb/)

---

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Specifications

- indoor/outdoor device
- Attention, LTC2-FT doesn't include temperature sensors, you need to purchase them separately
- Price LTC2-LB ca. CHF 66.- (07.04.2025) without sensors
- 2 External **Temperature** Sensors PT100 (support 3-wire), Cable Length 2m
- Power Supply: 1 built in 8500mAh Li-SOCI2 battery
  - Expected life time: depending on usage, 5 ... 10 years
- LoRaWAN version: 1.0.3
- LoRaWAN device class: A
- Protection: [IP66](https://en.wikipedia.org/wiki/IP_Code)
- Operating Temperature: -40 ... 85°C
- Size: 150 × 120 × 55 mm

---

## Documents/Links
- [Datasheet from dragino.com (2023-08-09)](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/dragino-ltc2-ft_02.pdf)
- [User Manual (online)](https://wiki.dragino.com/xwiki/bin/view/Main/User%20Manual%20for%20LoRaWAN%20End%20Nodes/LTC2%20-%20LoRaWAN%20Temperature%20Transmitter%20User%20Manual/)

---

## Ordering Info
**Attention:** This is an older procuct, the successor is the [LTC2-LB](https://hslu-ige-laes.github.io/lora-devices-ttn/docs/sensors/dragino-ltc2-lb/)

---

## Device specific Information

### Switch on the device
Out of the factory the device is switched off. To power on the LTC2-FT, open the case and set the jumper (connect the two pins):

![Switching between active and sleep mode (switch off / on, reset)](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/dragino-lsn50v2-d23_05.png "Power on the LSN50v2-D20")

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
10. Switch on the device, see above table

- After Configuration, the device restarts automatically and tries to join the network
- Now the device should join the network and you can see the incoming telegrams in the `Live data` section

---

## Optional Settings

### Change sampling interval
To change the sampling interval, you have to send the device configuration telegrams (Downlink-Messages)
The time interval in minutes at which the sensor queries the current values.

1. In the TTN Console on the device view, select the device and change to the tab `Messaging`, select `Downlink`
2. Change the `FPort to 2`
3. Copy/paste the payload, e.g. `01000258` into the `Payload` field to set interval to 10 minutes
4. Press `Send`
5. In the `Data` tab you should now see the scheduled telegram. The device only receives downlink data after a transmission. Therefore start a transmission by pressing the button on the back of the sensor (push once short, green led will illuminate)

#### Examples
'0100' is an identifier, the rest represents the sampling interval in hex

-	5 Minutes Interval:  '0100**012C**' (300s in hex are '012C')
-	10 Minutes Interval:  '0100**0258**' (600s in hex are '0258')
-	15 Minutes Interval: '0100**0384**' (900s in hex are '0384')
-	60 Minutes Interval: '0100**0E10**' (3600s in hex are '0E10')

---

## Payload Decoder

```javascript
function mapBatteryVoltageAbs(voltage) {
  if (voltage < 3.35) {
    return 0; // Critical
  } else if (voltage < 3.45) {
    return 1; // Warning
  } else if (voltage < 3.55) {
    return 2; // Good
  } else {
    return 3; // Very Good
  }
}

function decodeUplink(input) {
  const port = input.fPort;
  const bytes = input.bytes;
  const data = {};

  // Helpers
  const u16 = (hi, lo) => ((hi << 8) | lo) & 0xFFFF;
  const i16 = (hi, lo) => (hi << 24 >> 16) | lo; // sign-extended 16-bit

  const NULL_MARKER = 0x8001;

  const toTemperature_0x01 = (hi, lo) => {
    const raw = u16(hi, lo);
    if (raw === NULL_MARKER) return null;                 // device's NULL flag
    return parseFloat((i16(hi, lo) / 100).toFixed(2));    // °C with 0.01 resolution
  };

  const toTemperature_0x02 = (hi, lo) => {
    return parseFloat((i16(hi, lo) / 10).toFixed(1));     // °C with 0.1 resolution
  };

  if (port !== 2) {
    return { errors: ["Unsupported FPort"] };
  }

  // ---- Common header ----
  const ext = bytes[2] & 0x0F;                   // operating mode
  const pollMessageStatus = (bytes[2] & 0x40) >> 6;

  // Battery: top 14 bits (mask 0x3FFF), in volts
  const battery_volt_abs = (((bytes[0] << 8) | bytes[1]) & 0x3FFF) / 1000;

  data.battery_volt_abs = battery_volt_abs;
  data.battery_state_abs = mapBatteryVoltageAbs(battery_volt_abs);

  // ---- Payload by mode ----
  if (ext === 0x01) {
    // Temperature, 0.01 °C resolution, with NULL marker
    data["temperature_degrC_abs@channel1"] = toTemperature_0x01(bytes[3], bytes[4]);
    data["temperature_degrC_abs@channel2"] = toTemperature_0x01(bytes[5], bytes[6]);

  } else if (ext === 0x02) {
    // Temperature, 0.1 °C resolution
    data["temperature_degrC_abs@channel1"] = toTemperature_0x02(bytes[3], bytes[4]);
    data["temperature_degrC_abs@channel2"] = toTemperature_0x02(bytes[5], bytes[6]);

  }

  return { data };
}
```