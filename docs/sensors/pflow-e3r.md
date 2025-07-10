---
layout: default
title: pFlow - E3R
parent: Sensors
---

<img src="https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/pflow-e3r_01.jpg" width="250" align="right">

# pFlow - E3R
{: .no_toc }

- Manufacturer: <a href="https://www.pflowmeters.com/" target="_blank">pFlow</a>
- Product: <a href="https://www.pflowmeters.com/industries-of-ultrasonic-flowmeter/thermal-power-metering-flowmeter/e3r-heat-flow-meter-lora.html" target="_blank">E3R</a>

The E3R is a LoRaWAN Ultrasonic Heat Flow Meter.

---

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Specifications
- indoor device
- Price ca. CHF 350.- (03.06.2025)
- Built-in sensors
  - <b>Water Flow</b>, [m3/h]
- Pipe size range: DN20,DN25,DN32,DN40,DN50,DN65,DN80
- Power Supply: 10-36 VDC/500mA
- LoRaWAN version: 1.0.2
- LoRaWAN device class: A
- Modbus as well available
- Size: tbd
- Weight: tbd

---
## Documents
- tbd
---

## Ordering Info
- tbd

---

## Adding the Device to TTN
- The `JoinEUI`, `App EUI` and the `DevEUI` should be on a sticker on the cardboard box.
- Before a device can communicate via "The Things Network" we have to add it to an application.<br>

1. [Create a new application](https://hslu-ige-laes.github.io/lora-devices-ttn/docs/getting_started#create-a-new-application)
2. Under `End devices` in the application click `(+) Register end device`
3. Under `Input method` select `Enter end device specifics manually`
4. Under `Frequency plan` select `Europe 863-870 Mhz (SF9 for RX2 - recommended)`
5. Under `LoRaWAN version` select `1.0.2`, Regional Parameters version `RP001 Regional Parameters 1.0.2`
6. Under `JoinEUI` enter the `App EUI` from the App and press `Confirm`
7. Enter as well the `DevEUI` and the `AppKey` from the App
8. Set an end-device name
9. Press `Register end device`
10. Add the payload formatter from below, either to the device itself or if all devices in the app are from the same type, to the application
11. Plug in the device or restart LoRa in Communication settings

- Now the device should join the network and you can see the incoming telegrams in the `Live data` section

---

## Optional Settings

### Change TDC (Transmit Time Interval resp. Keep Alive Interval)
tbd
---

## Payload Decoder

```javascript
function decodeUplink(input) {
    var bytes = input.bytes;
    var fPort = input.fPort;
    
    var decoded = {};

    // Uplink payload decoding
    decoded.uplink = readUplinkInfo(bytes);
    
    return {
        data: decoded
    };
}

function readUplinkInfo(bytes) {
    var uplink = {};
    
    uplink["volumeFlow_m3perh_abs"] = readUInt32(0, bytes) / 100;
    uplink["volume_m3_inc"] = readUInt32(4, bytes) / 100;
    uplink["energy_kWh_inc@heating"] = readUInt32(13,bytes) /100;
    uplink["energy_kWh_inc@cooling"] = readUInt32(19,bytes) / 100;
    uplink["temperature_degrC_abs@return"] = readUInt16(11,bytes) / 100;
    uplink["temperature_degrC_abs@flow"] = readUInt16(17,bytes) / 100;
    uplink["velocity_mpers_abs"] = readUInt16(8, bytes) / 100;
    uplink["signalQuality_perc_abs"] = readUInt8(10, bytes);

    return uplink;
}

// helper functions

function readUInt32(index, bytes) {
    var value = (bytes[index] << 24 & 0xff000000) | (bytes[index + 1] << 16 & 0x00ff0000)  | (bytes[index + 2] << 8 & 0x0000ff00) | (bytes[index + 3] & 0x000000ff);
    return value & 0xffffffff;
}

function readUInt16(index, bytes) {
    var value = ((bytes[index] << 8) & 0xff00) | ((bytes[index + 1]) & 0x00ff);
    return value & 0xffff;
}

function readUInt8(index, bytes) {
    var value = ((bytes[index]) & 0xff);
    return value & 0xff;
}
```