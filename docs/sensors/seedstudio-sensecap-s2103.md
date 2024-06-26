---
layout: default
title: Seedstudio - SenseCap-S2103
parent: Sensors
---

<img src="https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/seedstudio-sensecap-s2103_01.jpg" width="250" align="right">

# Seedstudio - SenseCap-S2103
{: .no_toc }

- Manufacturer: <a href="https://www.seeedstudio.com/" target="_blank">Seedstudio</a>
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
- [S210X Sensor User Guide from seedstudio.com (2023-08-09)](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/seedstudio-sensecap-s2103_03.pdf)
- [Battery Life Prediction Table (Excel document)](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/seedstudio-sensecap-s2103_02.xlsx)

---

## Ordering Info
- Part Number: 114992869 Model S2103 
- [Ordering Link](https://www.bastelgarage.ch/sensecap-s2103-lorawan-co2-temperatur-und-luftfeuchtigkeitssensor?search=sensecap%20co2)

---

## Device specific Information

### LED States

![2-in-1 Configuration Button and LED](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/seedstudio-sensecap-s2103_04.png "Power on the SenseCap")

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
- Configure the device via Bluetooth with the `SenseCAP Mate App`. [See User Guide chapter 5.2 for details](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/seedstudio-sensecap-s2103_03.pdf)
- Copy the `JoinEUI`, `App EUI` and the `DevEUI` and send it from the smartphone via E-Mail to your computer.
- Before a device can communicate via "The Things Network" we have to add it to an application.<br>

1. [Create a new application](https://hslu-ige-laes.github.io/lora-devices-ttn/docs/getting_started#create-a-new-application)
2. Under `Overview` click `(+) Register device`
3. Under `Input method` select `Select the end device in the LoRaWAN Device Repository`
4. Enter the following device information
   - `End device brand` select `SenseCAP`
   - `Model` select `SenseCAP S2103...`
   - `Hardware Ver.` select `1.0` or whatever is possible or on the sticker
   - `Firmware` select `1.0` or whatever is possible or on the sticker
	 - `Profile (Region)` select `EU_863_870`
5. Under `Frequency plan` select `Europe 863-870 Mhz (SF9 for RX2 - recommended)`
6. Under `JoinEUI` enter the `App EUI` from the App
7. Enter as well the `DevEUI` and the `AppKey` from the sticker
8. Set an end-device name
9. Press `Register end device`
10. [Switch on the device](https://hslu-ige-laes.github.io/lora-devices-ttn/docs/seedstudio-sensecap-s2103#led-states)

- After Configuration, the device restarts automatically and tries to join the network
- Now the device should join the network and you can see the incoming telegrams in the `Live data` section
- The payload formatter should already be preset. If not, you can copy/paste it from below

---

## Payload formatter

```javascript
function hexToDec(hex) {
    return parseInt(hex, 16);
}

function isObjectEmpty(objectName) {
    return Object.keys(objectName).length === 0;
}

function stringToHex(str) {
    var result = '';
    for (var i = 0; i < str.length; i++) {
        result += str.charCodeAt(i).toString(16);
    }
    return result;
}

function bytes2HexString(arrBytes) {
    var str = '';
    for (var i = 0; i < arrBytes.length; i++) {
        var tmp;
        var num = arrBytes[i];
        if (num < 0) {
            tmp = (255 + num + 1).toString(16);
        } else {
            tmp = num.toString(16);
        }
        if (tmp.length === 1) {
            tmp = '0' + tmp;
        }
        str += tmp;
    }
    return str;
}

function decodeUplink(input) {
    try {
        var bytes = input['bytes'];
        let payload_raw = bytes2HexString(bytes).toUpperCase();
        var decoded = {};
        let payload = "";

        // CO2 value
        if (payload_raw.includes("010410")) {
            payload = payload_raw.split('010410')[1];
            if (payload.includes('010110')) {
                payload = payload.split('010110')[0];
            }
            payload = payload.substring(0, 8);
            payload = payload.match(/[a-fA-F0-9]{2}/g).reverse().join('');
            decoded.co2_ppm = Number(hexToDec(payload)) / 1000;
        }
        // Temperature
        if (payload_raw.includes("010110")) {
            payload = payload_raw.split('010110')[1];
            if (payload.includes('010210')) {
                payload = payload.split('010210')[0];
            }
            payload = payload.substring(0, 8);
            payload = payload.match(/[a-fA-F0-9]{2}/g).reverse().join('');
            decoded.temp_degrC = Number(hexToDec(payload)) / 1000;
        }
        // Humidity
        if (payload_raw.includes("010210")) {
            payload = payload_raw.split('010210')[1];
            if (payload_raw.length > 50) {
                if (payload.includes('0700')) {
                    payload = payload.split('0700')[0];
                }
            }
            payload = payload.substring(0, 8);
            payload = payload.match(/[a-fA-F0-9]{2}/g).reverse().join('');
            decoded.hum_relHum = Number(hexToDec(payload)) / 1000;
        }
        // Battery
        if ((payload_raw.includes("000700")) && (payload_raw.length > 18)) {
            payload = payload_raw.split('000700')[1];
            payload = payload.substring(0, 4);
            payload = payload.match(/[a-fA-F0-9]{2}/g).reverse().join('');
            decoded.battery_perc = Number(hexToDec(payload));
        }

        return { data: decoded };
    } catch (e) {
        // Handle error appropriately if needed
        return { data: null, error: e.message };
    }
}
```