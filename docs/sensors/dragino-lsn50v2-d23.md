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

---

## Adding the Device to TTN
- Before a device can communicate via "The Things Network" we have to add it to an application.<br>

1. [Create a new application](https://hslu-ige-laes.github.io/lora-devices-ttn/docs/getting_started#create-a-new-application)
2. Under `Overview` click `(+) Register device`
3. Under `Input method` select `Select the end device in the LoRaWAN Device Repository`
4. Enter the following device information
   - `End device brand` select `Dragino Technology Co., Limited`
   - `Model` select `LDS02`
   - `Hardware Ver.` select `Unknown Ver.` or whatever is possible or on the sticker
   - `Firmware` select `1.5` or whatever is possible or on the sticker
	 - `Profile (Region)` select `EU_863_870`
5. Under `Frequency plan` select `Europe 863-870 Mhz (SF9 for RX2 - recommended)`
6. Under `JoinEUI` enter the `App EUI` from the sticker
7. Enter as well the `DevEUI` and the `AppKey` from the sticker
8. Set an end-device name
9. Press `Register end device`
10. [Switch on the device](https://hslu-ige-laes.github.io/lora-devices-ttn/docs/dragino-lsn50v2-d23#switch-on-the-device)
11. Close the case

- Now the device should join the network and you can see the incoming telegrams in the `Live data` section
- The payload formatter should already be preset. If not, you can copy/paste it from below

---

## Payload formatter

```javascript
function Decoder(bytes, port) {
  var mode = (bytes[6] & 0x7C) >> 2;
  var decode = {};
  if ((mode != 2) && (mode != 31)) {
    decode.BatV = (bytes[0] << 8 | bytes[1]) / 1000;
    decode.TempC1 = parseFloat(((bytes[2] << 24 >> 16 | bytes[3]) / 10).toFixed(2));
    decode.ADC_CH0V = (bytes[4] << 8 | bytes[5]) / 1000;
    decode.Digital_IStatus = (bytes[6] & 0x02) ? "H" : "L";
    if (mode != 6) {
      decode.EXTI_Trigger = (bytes[6] & 0x01) ? "TRUE" : "FALSE";
      decode.Door_status = (bytes[6] & 0x80) ? "CLOSE" : "OPEN";
    }
  }
  if (mode == '0') {
    decode.Work_mode = "IIC";
    if ((bytes[9] << 8 | bytes[10]) === 0) {
      decode.Illum = (bytes[7] << 24 >> 16 | bytes[8]);
    } else {
      decode.TempC_SHT = parseFloat(((bytes[7] << 24 >> 16 | bytes[8]) / 10).toFixed(2));
      decode.Hum_SHT = parseFloat(((bytes[9] << 8 | bytes[10]) / 10).toFixed(1));
    }
  } else if (mode == '1') {
    decode.Work_mode = " Distance";
    decode.Distance_cm = parseFloat(((bytes[7] << 8 | bytes[8]) / 10).toFixed(1));
    if ((bytes[9] << 8 | bytes[10]) != 65535) {
      decode.Distance_signal_strength = parseFloat((bytes[9] << 8 | bytes[10]).toFixed(0));
    }
  } else if (mode == '2') {
    decode.Work_mode = " 3ADC";
    decode.BatV = bytes[11] / 10;
    decode.ADC_CH0V = (bytes[0] << 8 | bytes[1]) / 1000;
    decode.ADC_CH1V = (bytes[2] << 8 | bytes[3]) / 1000;
    decode.ADC_CH4V = (bytes[4] << 8 | bytes[5]) / 1000;
    decode.Digital_IStatus = (bytes[6] & 0x02) ? "H" : "L";
    decode.EXTI_Trigger = (bytes[6] & 0x01) ? "TRUE" : "FALSE";
    decode.Door_status = (bytes[6] & 0x80) ? "CLOSE" : "OPEN";
    if ((bytes[9] << 8 | bytes[10]) === 0) {
      decode.Illum = (bytes[7] << 24 >> 16 | bytes[8]);
    } else {
      decode.TempC_SHT = parseFloat(((bytes[7] << 24 >> 16 | bytes[8]) / 10).toFixed(2));
      decode.Hum_SHT = parseFloat(((bytes[9] << 8 | bytes[10]) / 10).toFixed(1));
    }
  } else if (mode == '3') {
    decode.Work_mode = "3DS18B20";
    decode.TempC2 = parseFloat(((bytes[7] << 24 >> 16 | bytes[8]) / 10).toFixed(2));
    decode.TempC3 = parseFloat(((bytes[9] << 24 >> 16 | bytes[10]) / 10).toFixed(1));
  } else if (mode == '4') {
    decode.Work_mode = "Weight";
    decode.Weight = (bytes[7] << 24 >> 16 | bytes[8]);
  } else if (mode == '5') {
    decode.Work_mode = "Count";
    decode.Count = (bytes[7] << 24 | bytes[8] << 16 | bytes[9] << 8 | bytes[10]);
  } else if (mode == '31') {
    decode.Work_mode = "ALARM";
    decode.BatV = (bytes[0] << 8 | bytes[1]) / 1000;
    decode.TempC1 = parseFloat(((bytes[2] << 24 >> 16 | bytes[3]) / 10).toFixed(2));
    decode.TempC1MIN = bytes[4] << 24 >> 24;
    decode.TempC1MAX = bytes[5] << 24 >> 24;
    decode.SHTEMPMIN = bytes[7] << 24 >> 24;
    decode.SHTEMPMAX = bytes[8] << 24 >> 24;
    decode.SHTHUMMIN = bytes[9];
    decode.SHTHUMMAX = bytes[10];
  }
  if ((bytes.length == 11) || (bytes.length == 12)) {
    return decode;
  }
}
```