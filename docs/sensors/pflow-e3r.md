---
layout: default
title: pFlow - E3R
parent: Sensors
---

<img src="https://hslu-ige-laes.github.io/lora-devices-ttn/docs/sensors/pflow-e3r_01.jpg" width="250" align="right">

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
- LoRaWAN device class: C
- Modbus as well available

---
## Documents
- tbd
---

## Ordering Info
- tbd

---

## Settings (mandatory!)

1. On the device, go to `Device Settings / 3. Communication / 4. Lora USC` and set it to `868100000 Hz`
   - you can enter the `Device Settings` by pressing the button with the three lines on the device
2. Make shure, that in ttn `Device Class C` is configured under `Network layer`
3. Reset Lora (in `Device Settings / 3. Communication / 8. Reset Lora`) 
4. Mount the device on the pipe
   - use a lot of ultrasonic contact gel on the ultrasonic contacts
   - mount as well the temperature sensors with thermal paste
5. Set the pipe Diameter, Wall Thickness and Pipe Material (in `Device Settings / 1. Pipe Parameter`)
   - the thickness could also be measured with a separate ultrasonic thickness sensor
6. Stop the water flow in the hydraulic circuit and make a zero set (in `Device Settings / 5. Select Setting / 4. Reset Zero`)
7. Start the water flow, done
8. If possible (recommended):
   - Make a paralell measurement with a high quality ultrasonic flow meter
   - Calculate the correction factor and set it in the device (in `Device Settings / 6. Calibration / 1. Scale Factor`)

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