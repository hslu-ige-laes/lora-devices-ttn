---
layout: default
title: Dragino - LSN50v2-D23
parent: Sensors
---

<img src="https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/dragino-lsn50v2-d23_01.jpg" width="250" align="right">

# Dragino - LSN50v2-D23
{: .no_toc }

- Manufacturer: <a href="https://www.dragino.com/" target="_blank">Dragino</a>
- Product: <a href="https://www.dragino.com/products/temperature-humidity-sensor/item/193-lsn50v2-d23.html" target="_blank">LSN50v2-D23</a>

The LSN50v2-D23 is a LoRaWAN Waterproof Outdoor Temperature Sensor with 3 external Probes.

---

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Specifications

- indoor/outdoor device
- Price ca. CHF 70.- (09.08.2023)
- 3 External Sensors
  - **Temperature**, -55 ... +125 [°C], ± 0.5 (max ±2.0 °C)
- Power Supply: 1 built in 8500mAh Li-SOCI2 battery
  - Expected life time: depending on usage, 5 ... 10 years
- LoRaWAN version: 1.0.3
- LoRaWAN device class: A
- Protection: [IP66](https://en.wikipedia.org/wiki/IP_Code)
- Operating Temperature: -40 ... 85°C
- Size: 124 × 63 × 42 mm
- Weight: 295 g

---

## Documents/Links
- [Payload description (2023-08-09)](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/dragino-lsn50v2-d23_04.txt)
- [Datasheet from dragino.com (2023-08-09)](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/dragino-lsn50v2-d23_03.pdf)
- [User Manual (online)](http://wiki.dragino.com/xwiki/bin/view/Main/User%20Manual%20for%20LoRaWAN%20End%20Nodes/LSN50v2-D20-D22-D23%20LoRaWAN%20Temperature%20Sensor%20User%20Manual)
- [Datasheet Temperaturesensor DS18B20](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/dragino-lsn50v2-d23_02.pdf)

---

## Ordering Info
- Part Number: LSN50v2-D23-EU868
- [Ordering Link](https://www.bastelgarage.ch/lsn50v2-d23-lorawan-3-kanal-temperatursensor-node-868mhz?search=lsn50%20v2)

---

## Device specific Information

### Switch on the device
Out of the factory the device is switched off. To power on the LSN50v2-D20, open the case and set the jumper (connect the two pins):

![Switching between active and sleep mode (switch off / on, reset)](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/dragino-lsn50v2-d23_05.png "Power on the LSN50v2-D20")

### Channel Mapping
- Channel 1: Red
- Channel 2: White
- Channel 3: Black

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
10. [Switch on the device](https://hslu-ige-laes.github.io/lora-devices-ttn/docs/dragino-lsn50v2-d23#switch-on-the-device)
11. Close the case

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
function decodeUplink(input) {
  var port = input.fPort;
  var bytes = input.bytes;
  var mode = (bytes[6] & 0x7C) >> 2;
  var data = {};

  switch (port) {
    case 2:
      if (mode == '3') {
        data.battery_volt_abs = (bytes[0] << 8 | bytes[1]) / 1000;
        data.alarm_state_abs = (bytes[6] & 0x01) ? 1 : 0;

        if ((bytes[2] == 0xff) && (bytes[3] == 0xff)) {
          data["temperature_degrC_abs@channel1"] = 32767.0;
        } else {
          data["temperature_degrC_abs@channel1"] = parseFloat(((bytes[2] << 24 >> 16 | bytes[3]) / 10).toFixed(1));
        }

        if ((bytes[7] == 0xff) && (bytes[8] == 0xff)) {
          data["temperature_degrC_abs@channel2"] = 32767.0;
        } else {
          data["temperature_degrC_abs@channel2"] = parseFloat(((bytes[7] << 24 >> 16 | bytes[8]) / 10).toFixed(1));
        }

        if ((bytes[9] == 0xff) && (bytes[10] == 0xff)) {
          data["temperature_degrC_abs@channel3"] = 32767.0;
        } else {
          data["temperature_degrC_abs@channel3"] = parseFloat(((bytes[9] << 24 >> 16 | bytes[10]) / 10).toFixed(1));
        }
      }

      if (bytes.length == 11) {
        return {
          data: data,
        };
      }
      break;

    default:
      return {
        errors: ["unknown FPort"]
      };
  }
}
```