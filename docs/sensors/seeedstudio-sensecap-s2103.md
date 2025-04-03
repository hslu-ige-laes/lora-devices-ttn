---
layout: default
title: Seeedstudio - SenseCap-S2103
parent: Sensors
---

<img src="https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/seeedstudio-sensecap-s2103_01.jpg" width="250" align="right">

# Seeedstudio - SenseCap-S2103
{: .no_toc }

- Manufacturer: <a href="https://www.seeedstudio.com/" target="_blank">Seeedstudio</a>
- Product: <a href="https://www.seeedstudio.com/SenseCAP-S2103-LoRaWAN-CO2-Temperature-and-Humidity-Sensor-p-5356.html" target="_blank">SenseCap S2103</a>

The SenseCap S2103 is a LoRaWAN indoor/outdoor sensor to measure temperature, humidity and CO<sub>2</sub>

---

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Specifications

- indoor/outdoor device
- Price ca. CHF 145.- (09.08.2023)
- Sensors
  - **Temperature**, -40 ... +85 [°C], ± 0.2 °C, Resolution 0.01 °C, Long term drift < 0.03 °C/year
  - **relative Humidity**, 0 ... 100[%rH], ± 1.8, Resolution 0.01 %rH, Long term drift < 0.25 %RH/year 
  - **CO<sub>2</sub>**, 400 ... 10'000 [ppm], ±(30 ppm +3% of reading) , extended range ±10% of reading, Resolution 1 ppm
- Power Supply: 1 Li-SOCl2, ER34615, 3.6V, 19'000 mAh
  - Expected life time: depending on usage, 5 ... 10 years
- LoRaWAN version: 1.0.3
- LoRaWAN device class: A
- Protection: [IP66](https://en.wikipedia.org/wiki/IP_Code)
- Operating Temperature: 0 ... +50 °C ( Effective measurement range of CO<sub>2</sub>)
- Size: 184.2 × 63 × 63.7 mm
- Weight: 452 g

---

## Documents/Links
- [S210X Sensor User Guide from seeedstudio.com (2023-08-09)](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/seeedstudio-sensecap-s2103_03.pdf)
- [Battery Life Prediction Table (Excel document)](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/seeedstudio-sensecap-s2103_02.xlsx)

---

## Ordering Info
- Part Number: 114992869 Model S2103 
- [Ordering Link](https://www.bastelgarage.ch/sensecap-s2103-lorawan-co2-temperatur-und-luftfeuchtigkeitssensor?search=sensecap%20co2)

---

## Device specific Information

### LED States

![2-in-1 Configuration Button and LED](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/seeedstudio-sensecap-s2103_04.png "Power on the SenseCap")

| Action | Description | Green LED Status |
|--------|-------------|------------------|
| First power up, press and hold for 3s      | Power on and activate the Bluetooth           | LED flashes at 1s frequency, waiting for Bluetooth connection. If Bluetooth not connected within 1 minute, the device will shut downagain |
| Press once      | Reboot device and join LoRa network           | 1. The LEDw ill be onfor 5 seconds for initialization <br>2. Waiting to joinLoRa network: breathing light flashing <br>3. Join LoRa network success: LED flashes fast for 2s <br>4. LoRa network joinfailure: LEDsuddenlystop.                |
| Press and hold for 3s      | Activate Bluetooth again           | 1. Waiting for Bluetooth connection: LEDflashes at 1s frequency <br>2. Enter configurationmode after Bluetooth connection is successful: LED flashes at 2s frequency <br><br>If Bluetooth is not connected within 1 minute, the device will reboot and join Lora network.                |
| Press and hold for 9s      |  Power off           | In the 3rd seconds will start flashing at 1s frequency, until the light is steady on, release the button, the light will go out.                |

**Note**<br>
After power off, you need to reconfigure the frequency band. Power off is recommended when not deployed.

---

## Adding the Device to TTN
- Configure the device via Bluetooth with the `SenseCAP Mate App`. [See User Guide chapter 5.2 for details](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/seeedstudio-sensecap-s2103_03.pdf)
- Copy the `JoinEUI`, `App EUI` and the `DevEUI` and send it from the smartphone via E-Mail to your computer.
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
9. [Switch on the device](https://hslu-ige-laes.github.io/lora-devices-ttn/docs/seeedstudio-sensecap-s2103#led-states)

- After Configuration, the device restarts automatically and tries to join the network
- Now the device should join the network and you can see the incoming telegrams in the `Live data` section
- The payload formatter you can copy/paste from below

---

## Change Device Settings
- Configure the device via Bluetooth with the `SenseCAP Mate App`. [See User Guide chapter 5.2 for details](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/seeedstudio-sensecap-s2103_03.pdf)

1. Connect to the device
2. Choose configuration mode `Device Firmware Update` and Update the Firmware
3. Connect again after update and choose under configuration mode `Advanced Configuration`
4. Go to tab `Settings`
5. Under `Platform` choose `The Things Network` (Attention, dont choose "SenceCAP for The Things Network")
6. Under `Frequency Plan` choose `EU868`
7. Under `Upling Interval (min)` choose `10`
8. Under `Activation Type` choose `OTAA` (Over the air activation)
9. Press `Send`

- After Configuration, the device restarts automatically and tries to join the network

---
## Payload formatter

```javascript
function hexToDec(hex) {
    return parseInt(hex, 16);
}

function extractValue(payloadRaw, tag, nextTag = null, byteLength = 4) {
    if (!payloadRaw.includes(tag)) return null;

    let segment = payloadRaw.split(tag)[1];
    if (nextTag && segment.includes(nextTag)) {
        segment = segment.split(nextTag)[0];
    }

    const expectedLength = byteLength * 2;
    segment = segment.substring(0, expectedLength);

    const bytePairs = segment.match(/[a-fA-F0-9]{2}/g);
    if (!bytePairs || bytePairs.length !== byteLength) return null;

    const reversed = bytePairs.reverse().join('');
    return hexToDec(reversed);
}

function bytesToHexString(bytes) {
    return bytes.map(b => b.toString(16).padStart(2, '0')).join('').toUpperCase();
}

function decodeUplink(input) {
    try {
        const bytes = input.bytes;
        const payloadRaw = bytesToHexString(bytes);
        const decoded = {};

        // CO2 (010410 ... /1000)
        const co2Val = extractValue(payloadRaw, "010410", "010110", 4);
        if (co2Val !== null) {
            decoded.co2_ppm = co2Val / 1000;
        }

        // Temperature (010110 ... /1000)
        const tempVal = extractValue(payloadRaw, "010110", "010210", 4);
        if (tempVal !== null) {
            decoded.temp_degrC = tempVal / 1000;
        }

        // Humidity (010210 ... /1000)
        const humVal = extractValue(payloadRaw, "010210", "0700", 4);
        if (humVal !== null) {
            decoded.hum_relHum = humVal / 1000;
        }

        // Battery (000700 ... %, 2 bytes)
        const battVal = extractValue(payloadRaw, "000700", null, 2);
        if (battVal !== null) {
            decoded.battery_perc = battVal;
        }

        // Optional: Debug raw payload (for dev/console)
        // decoded._debug_payloadRaw = payloadRaw;

        return { data: decoded };
    } catch (e) {
        return { data: null, error: e.message };
    }
}
```