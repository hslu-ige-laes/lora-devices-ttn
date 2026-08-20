---
layout: default
title: Dragino - LSN50v2-D20
parent: Sensors
---

<img src="https://hslu-ige-laes.github.io/lora-devices-ttn/docs/sensors/dragino-lsn50v2-d20_01.jpg" width="250" align="right">

# Dragino - LSN50v2-D20
{: .no_toc }

- Manufacturer: <a href="https://www.dragino.com/" target="_blank">Dragino</a>
- Product: <a href="https://www.dragino.com/products/lora-lorawan-end-node/item/168-lsn50v2-d20.html" target="_blank">LSN50v2-D20</a>

The LSN50v2-D20 is a LoRaWAN Waterproof Outdoor Temperature Sensor with 1 external Probe.

---

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Specifications

- indoor/outdoor device
- 1 External Sensor (DS18B20, cable length 2 m)
  - **Temperature**, -55 ... +125 [°C], ± 0.5 (max ±2.0 °C)
- Power Supply: 1 built in 8500mAh Li-SOCI2 battery
  - Expected life time: depending on usage, 5 ... 10 years
- LoRaWAN version: 1.0.3
- LoRaWAN device class: A
- Protection: [IP66](https://en.wikipedia.org/wiki/IP_Code)
- Operating Temperature: -40 ... 85°C
- Size: 124 × 63 × 42 mm

---

## Documents/Links
- [Payload description (2023-08-09)](https://hslu-ige-laes.github.io/lora-devices-ttn/docs/sensors/dragino-lsn50v2-d20_04.txt)
- [Datasheet from dragino.com (2026-07-17)](https://hslu-ige-laes.github.io/lora-devices-ttn/docs/sensors/dragino-lsn50v2-d20_03.pdf)
- [User Manual (online)](http://wiki.dragino.com/xwiki/bin/view/Main/User%20Manual%20for%20LoRaWAN%20End%20Nodes/LSN50v2-D20-D22-D23%20LoRaWAN%20Temperature%20Sensor%20User%20Manual)
- [Datasheet Temperaturesensor DS18B20](https://hslu-ige-laes.github.io/lora-devices-ttn/docs/sensors/dragino-lsn50v2-d20_02.pdf)

---

## Ordering Info
- Part Number: LSN50v2-D20-EU868
- Remark: The LSN50v2-D20 is phased out at most distributors. The successor is the [Dragino D20-LB](https://www.bastelgarage.ch/d20-lb-lorawan-temperatursensor-node-868mhz) (ca. CHF 65.-, 17.07.2026)
  - Attention: The D20-LB uses a different payload format, so this page and its payload decoder do not apply to it.

---

## Device specific Information

### Switch on the device
Out of the factory the device is switched off. To power on the LSN50v2-D20, open the case and set the jumper (connect the two pins):

![Switching between active and sleep mode (switch off / on, reset)](https://hslu-ige-laes.github.io/lora-devices-ttn/docs/sensors/dragino-lsn50v2-d20_05.png "Power on the LSN50v2-D20")

---

## Adding the Device to TTN
- The `JoinEUI`, `App EUI` and the `DevEUI` should be on a sticker on the cardboard box.
- Before a device can communicate via "The Things Network" we have to add it to an application.<br>

1. [Create a new application](https://hslu-ige-laes.github.io/lora-devices-ttn/docs/getting_started#create-a-new-application)
2. Under `End devices` in the application click `(+) Register end device`
3. Under `Input method` select `Enter end device specifics manually`
4. Under `Frequency plan` select `Europe 863-870 Mhz (SF9 for RX2 - recommended)`
5. Under `LoRaWAN version` select `1.0.3`
6. Under `JoinEUI` enter the `App EUI` from the App and press `Confirm`
7. Enter as well the `DevEUI` and the `AppKey` from the App
8. Set an end-device name
9. Press `Register end device`
10. Add the payload formatter from below, either to the device itself or if all devices in the app are from the same type, to the application
11. [Switch on the device](#switch-on-the-device)
12. Close the case

- After Configuration, the device restarts automatically and tries to join the network
- Now the device should join the network and you can see the incoming telegrams in the `Live data` section

---

## Optional Settings

### Change sampling interval
Out of the factory the device measures and transmits every 20 minutes.

To change the sampling interval, you have to send the device configuration telegrams (Downlink-Messages).
The interval is the time in **seconds** (hex encoded) at which the sensor measures and transmits the current values.

1. In the TTN Console on the device view, select the device and change to the tab `Messaging`, select `Downlink`
2. Change the `FPort to 2`
3. Copy/paste the payload, e.g. `01000258` into the `Payload` field to set interval to 10 minutes
4. Press `Send`
5. In the `Data` tab you should now see the scheduled telegram. The device only receives downlink data after an uplink. Therefore either wait for the next periodic uplink or open the case and press the `ACT` button on the PCB once short to trigger an uplink

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

function decodeTemp(hi, lo) {
  if (hi === 0xff && lo === 0xff) {
    return 32767.0; // kein Sensor angeschlossen
  }
  return parseFloat(((hi << 24 >> 16 | lo) / 10).toFixed(1));
}

function decodeUplink(input) {
  var port = input.fPort;
  var bytes = input.bytes;
  var data = {};

  if (port !== 2) {
    return { errors: ["unknown FPort"] };
  }

  if (bytes.length !== 11) {
    return { errors: ["unexpected payload length"] };
  }

  var mode = (bytes[6] & 0x7C) >> 2;
  data.mode = mode;

  // Bytes 0-6 sind in Mode 0, 1, 3, 4, 5 identisch aufgebaut
  data.battery_volt_abs = (bytes[0] << 8 | bytes[1]) / 1000;
  data.battery_state_abs = mapBatteryVoltageAbs(data.battery_volt_abs);
  //data.alarm_state_abs = (bytes[6] & 0x01) ? 1 : 0;
  //data.digital_input_abs = (bytes[6] & 0x02) ? 1 : 0;
  //data.adc_volt_abs = (bytes[4] << 8 | bytes[5]) / 1000;
  data.temperature_degrC_abs = decodeTemp(bytes[2], bytes[3]);

  if (mode === 3) {
    // 3DS18B20-Modus: zwei weitere Temperaturen
    //data.temperature2_degrC_abs = decodeTemp(bytes[7], bytes[8]);
    //data.temperature3_degrC_abs = decodeTemp(bytes[9], bytes[10]);
  }

  return { data: data };
}
```
