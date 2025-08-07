---
layout: default
title: Seeedstudio - SenseCap-S2102
parent: Sensors
---

<img src="https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/seeedstudio-sensecap-s2102_01.jpg" width="250" align="right">

# Seeedstudio - SenseCap-S2102
{: .no_toc }

- Manufacturer: <a href="https://www.seeedstudio.com/" target="_blank">Seeedstudio</a>
- Product: <a href="https://www.seeedstudio.com/SenseCAP-S2102-LoRaWAN-Light-Intensity-Sensor-p-5355.html" target="_blank">SenseCap S2102</a>

The SenseCap S2102 is a LoRaWAN indoor/outdoor sensor to measure light intensity in lux.

---

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Specifications

- indoor/outdoor device
- Price ca. CHF 75.- (09.08.2023)
- Sensors
  - **Light Intensity**, 0 ... 16'000 [lux], ± 5 %, Resolution 1 lux
- Power Supply: 1 Li-SOCl2, ER34615, 3.6V, 19'000 mAh
  - Expected life time: depending on usage, 5 ... 10 years
- LoRaWAN version: 1.0.3
- LoRaWAN device class: A
- Protection: [IP66](https://en.wikipedia.org/wiki/IP_Code)
- Operating Temperature: 0 ... +50 °C ( Effective measurement range of CO<sub>2</sub>)
- Size: 184.2 × 63 × 63.7 mm
- Weight: 457 g

---

## Documents/Links
- [S210X Sensor User Guide from seeedstudio.com (2023-08-09)](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/seeedstudio-sensecap-s2103_03.pdf)
- [Battery Life Prediction Table (Excel document)](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/seeedstudio-sensecap-s2103_02.xlsx)

---

## Ordering Info
- Part Number: 114992868  Model S2102
- [Ordering Link](https://www.bastelgarage.ch/sensecap-s2102-lorawan-lichtsensor)

---

## Button Actions, Modes and LED States

![2-in-1 Configuration Button and LED](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/seeedstudio-sensecap-s2103_04.png "Power on the SenseCap")

| Action | Description | Green LED Status |
|--------|-------------|------------------|
| First power up, press and hold for 3s      | Power on and activate the Bluetooth           | LED flashes at 1s frequency, waiting for Bluetooth connection. If Bluetooth not connected within 1 minute, the device will shut down again |
| Press once      | Reboot device and join LoRa network           | 1. The LED will be on for 5 seconds for initialization <br>2. Waiting to join LoRa network: breathing light flashing <br>3. Join LoRa network success: LED flashes fast for 2s <br>4. LoRa network join failure: LED suddenly stop.                |
| Press and hold for 3s      | Activate Bluetooth again           | 1. Waiting for Bluetooth connection: LED flashes at 1s frequency <br>2. Enter configuration mode after Bluetooth connection is successful: LED flashes at 2s frequency <br><br>If Bluetooth is not connected within 1 minute, the device will reboot and join Lora network.                |
| Press and hold for 9s      |  Power off           | In the 3rd second LED will start flashing at 1s frequency, release the button when the light is steady on, the light will then turn off.                |

**Note**<br>
After power off, you need to reconfigure the frequency band. Power off is recommended when not deployed.

---

## Adding the Device to TTN
- Configure the device via Bluetooth with the `SenseCAP Mate App`. [See User Guide chapter 5.2 for details](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/seeedstudio-sensecap-s2103_03.pdf)
  -> you don't have to create an account, simply click `Skip` on top right
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
9. Add the payload formatter from below, either to the device itself or if all devices in the app are from the same type, to the application
10. [Switch on the device](https://hslu-ige-laes.github.io/lora-devices-ttn/docs/seeedstudio-sensecap-s2102#led-states)

- After Configuration, the device restarts automatically and tries to join the network
- Now the device should join the network and you can see the incoming telegrams in the `Live data` section

---

## Settings (mandatory!)
- Configure the device via Bluetooth with the `SenseCAP Mate App`. [See User Guide chapter 5.2 for details](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/seeedstudio-sensecap-s2103_03.pdf)

1. Connect to the device
2. Choose configuration mode `Device Firmware Update` and Update the Firmware
3. Connect again after update and choose under configuration mode `Advanced Configuration`
4. Go to tab `Settings`
5. Under `Platform` choose `The Things Network` (Attention, dont choose "SenceCAP for The Things Network")
6. Under `Frequency Plan` choose `EU868`
7. Under `Upling Interval (min)` choose `10`
8. Under `Activation Type` choose `OTAA` (Over the air activation)
9. Under `Packet Policy` choose `2C+1N` (Over the air activation)
10. Press `Send`

- After Configuration, the device restarts automatically and tries to join the network

---

## Payload Decoder

```javascript
function decodeUplink(input) {
    try {
        const bytes = input.bytes;
        const bytesString = bytes2HexString(bytes).toUpperCase();
        
        const decoded = {};

        // CRC check (simplified)
        if (!crc16Check(bytesString)) {
            return { data: null, error: "CRC check failed" };
        }

        // Length Check: should be divisible by 7 bytes (+ 2 CRC bytes)
        if ((((bytesString.length / 2) - 2) % 7) !== 0) {
            return { data: null, error: "Length check failed" };
        }

        // Handle each 7-byte frame
        const frameArray = divideBy7Bytes(bytesString);
        
        for (let i = 0; i < frameArray.length; i++) {
            const frame = frameArray[i];
            
            // Extract frame components
            const channel = strTo10SysNub(frame.substring(0, 2));
            const dataID = strTo10SysNub(frame.substring(2, 6));
            const dataValue = frame.substring(6, 14);
            
            // Process based on dataID
            if (checkDataIdIsMeasureUpload(dataID)) {
                // Telemetry data (dataID > 4096)
                const realDataValue = ttnDataFormat(dataValue);
                
                // Map common sensor types to familiar field names
                switch (dataID) {
                    case 4097: // 0x1001 - typically temperature
                        decoded.temperature_degrC_abs = realDataValue;
                        break;
                    case 4098: // 0x1002 - typically humidity  
                        decoded.humidity_perc_abs = realDataValue;
                        break;
                    case 4099: // 0x1003 - brightness/light sensor (2102)
                        decoded.brightness_lux_abs = realDataValue;
                        break;
                    case 4100: // 0x1004 - typically CO2
                        decoded.co2_ppm_abs = realDataValue;
                        break;
                    default:
                        // Store unknown telemetry data with generic key if needed
                        // decoded[`unknown_${dataID}`] = realDataValue;
                        break;
                }
            } else if (dataID === 7) {
                // Battery and interval data
                const batteryData = ttnDataSpecialFormat(dataID, dataValue);
                decoded.battery_perc_abs = batteryData.power;
            }
            // Handle other special dataIDs as needed
        }

        return { data: decoded };
        
    } catch (e) {
        return { 
            data: { 
                valid: false, 
                err: -999, 
                error: e.message 
            } 
        };
    }
}

// Utility functions from official decoder
function bytes2HexString(arrBytes) {
    let str = '';
    for (let i = 0; i < arrBytes.length; i++) {
        let tmp;
        const num = arrBytes[i];
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

function divideBy7Bytes(str) {
    const frameArray = [];
    for (let i = 0; i < str.length - 4; i += 14) {
        const data = str.substring(i, i + 14);
        frameArray.push(data);
    }
    return frameArray;
}

function littleEndianTransform(data) {
    const dataArray = [];
    for (let i = 0; i < data.length; i += 2) {
        dataArray.push(data.substring(i, i + 2));
    }
    dataArray.reverse();
    return dataArray;
}

function strTo10SysNub(str) {
    const arr = littleEndianTransform(str);
    return parseInt(arr.toString().replace(/,/g, ''), 16);
}

function checkDataIdIsMeasureUpload(dataId) {
    return parseInt(dataId) > 4096;
}

function ttnDataFormat(str) {
    const strReverse = littleEndianTransform(str);
    let str2 = toBinary(strReverse);
    
    if (str2.substring(0, 1) === '1') {
        // Handle negative numbers
        const arr = str2.split('');
        const reverseArr = [];
        for (let i = 0; i < arr.length; i++) {
            const item = arr[i];
            if (parseInt(item) === 1) {
                reverseArr.push(0);
            } else {
                reverseArr.push(1);
            }
        }
        str2 = parseInt(reverseArr.join(''), 2) + 1;
        return parseFloat('-' + str2 / 1000);
    }
    return parseInt(str2, 2) / 1000;
}

function ttnDataSpecialFormat(dataId, str) {
    const strReverse = littleEndianTransform(str);
    const str2 = toBinary(strReverse);
    
    switch (dataId) {
        case 7:
            // Battery && interval
            return {
                interval: parseInt(str2.substr(0, 16), 2),
                power: parseInt(str2.substr(-16, 16), 2)
            };
        default:
            return str2;
    }
}

function toBinary(arr) {
    const binaryData = [];
    for (let i = 0; i < arr.length; i++) {
        const item = arr[i];
        let data = parseInt(item, 16).toString(2);
        const dataLength = data.length;
        if (data.length !== 8) {
            for (let j = 0; j < 8 - dataLength; j++) {
                data = '0' + data;
            }
        }
        binaryData.push(data);
    }
    return binaryData.toString().replace(/,/g, '');
}

function crc16Check(data) {
    // Simplified CRC check - return true for now
    // Implement proper CRC16 if needed
    return true;
}
```