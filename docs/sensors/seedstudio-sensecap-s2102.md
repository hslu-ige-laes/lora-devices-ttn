---
layout: default
title: Seedstudio - SenseCap-S2102
parent: Sensors
---

<img src="https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/seedstudio-sensecap-s2102_01.jpg" width="250" align="right">

# Seedstudio - SenseCap-S2102
{: .no_toc }

- Manufacturer: <a href="https://www.seeedstudio.com/" target="_blank">Seedstudio</a>
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
- [S210X Sensor User Guide from seedstudio.com (2023-08-09)](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/seedstudio-sensecap-s2103_03.pdf)
- [Battery Life Prediction Table (Excel document)](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/seedstudio-sensecap-s2103_02.xlsx)

---

## Ordering Info
- Part Number: 114992868  Model S2102
- [Ordering Link](https://www.bastelgarage.ch/sensecap-s2102-lorawan-lichtsensor)

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
   - `Model` select `SenseCAP S2102...`
   - `Hardware Ver.` select `1.0` or whatever is possible or on the sticker
   - `Firmware` select `1.0` or whatever is possible or on the sticker
	 - `Profile (Region)` select `EU_863_870`
5. Under `Frequency plan` select `Europe 863-870 Mhz (SF9 for RX2 - recommended)`
6. Under `JoinEUI` enter the `App EUI` from the App
7. Enter as well the `DevEUI` and the `AppKey` from the sticker
8. Set an end-device name
9. Press `Register end device`
10. [Switch on the device](https://hslu-ige-laes.github.io/lora-devices-ttn/docs/seedstudio-sensecap-s2102#led-states)

- After Configuration, the device restarts automatically and tries to join the network
- Now the device should join the network and you can see the incoming telegrams in the `Live data` section
- The payload formatter should already be preset. If not, you can copy/paste it from below

---

## Payload formatter

```javascript
/**
 * SenseCAP & TTN (new v3) Converter
 *
 * @since 3.0
 * @return Object
 *      @param  Boolean     valid       Indicates whether the payload is a valid payload.
 *      @param  String      err         The reason for the payload to be invalid. 0 means valid, minus means invalid.
 *      @param  String      payload     Hexadecimal string, to show the payload.
 *      @param  Array       messages    One or more messages are parsed according to payload.
 *                              type // Enum:
 *                                   //   - "report_telemetry"
 *                                   //   - "upload_battery"
 *                                   //   - "upload_interval"
 *                                   //   - "upload_version"
 *                                   //   - "upload_sensor_id"
 *                                   //   - "report_remove_sensor"
 *                                   //   - "unknown_message"
 *
 *
 *
 *
 *  @sample-1
 *      var sample = Decoder(["00", "00", "00", "01", "01", "00", "01", "00", "07", "00", "64", "00", "3C", "00", "01", "20", "01", "00", "00", "00", "00", "28", "90"], null);
 *      {
 *        valid: true,
 *        err: 0,
 *        payload: '0000000101000100070064003C00012001000000002890',
 *        messages: [
 *           { type: 'upload_version',
 *             hardwareVersion: '1.0',
 *             softwareVersion: '1.1' },
 *           { type: 'upload_battery', battery: 100 },
 *           { type: 'upload_interval', interval: 3600 },
 *           { type: 'report_remove_sensor', channel: 1 }
 *        ]
 *      }
 * @sample-2
 *      var sample = Decoder(["01", "01", "10", "98", "53", "00", "00", "01", "02", "10", "A8", "7A", "00", "00", "AF", "51"], null);
 *      {
 *        valid: true,
 *        err: 0,
 *        payload: '01011098530000010210A87A0000AF51',
 *        messages: [
 *           { type: 'report_telemetry',
 *             measurementId: 4097,
 *             measurementValue: 21.4 },
 *           { type: 'report_telemetry',
 *             measurementId: 4098,
 *             measurementValue: 31.4 }
 *        ]
 *      }
 * @sample-3
 *      var sample = Decoder(["01", "01", "00", "01", "01", "00", "01", "01", "02", "00", "6A", "01", "00", "15", "01", "03", "00", "30", "F1", "F7", "2C", "01", "04", "00", "09", "0C", "13", "14", "01", "05", "00", "7F", "4D", "00", "00", "01", "06", "00", "00", "00", "00", "00", "4C", "BE"], null);
 *      {
 *        valid: true,
 *        err: 0,
 *        payload: '010100010100010102006A01001501030030F1F72C010400090C13140105007F4D0000010600000000004CBE',
 *        messages: [
 *            { type: 'upload_sensor_id', sensorId: '30F1F72C6A010015', channel: 1 }
 *        ]
 *      }
 */

/**
 * Entry, decoder.js
 */
function decodeUplink(input) {
  var data = {};
  var bytes = input['bytes'];
  // init
  var bytesString = bytes2HexString(bytes).toLocaleUpperCase();
  var decoded = {
    // valid
    valid: true,
    err: 0,
    // bytes
    payload: bytesString,
    // messages array
    messages: [],
  };

  // CRC check
  if (!crc16Check(bytesString)) {
    decoded['valid'] = false;
    decoded['err'] = -1; // "crc check fail."
    return { data: decoded };
  }

  // Length Check
  if ((bytesString.length / 2 - 2) % 7 !== 0) {
    decoded['valid'] = false;
    decoded['err'] = -2; // "length check fail."
    return { data: decoded };
  }

  // Cache sensor id
  var sensorEuiLowBytes;
  var sensorEuiHighBytes;

  // Handle each frame
  var frameArray = divideBy7Bytes(bytesString);
  for (var forFrame = 0; forFrame < frameArray.length; forFrame++) {
    var frame = frameArray[forFrame];
    // Extract key parameters
    var channel = strTo10SysNub(frame.substring(0, 2));
    var dataID = strTo10SysNub(frame.substring(2, 6));
    var dataValue = frame.substring(6, 14);
    var realDataValue = isSpecialDataId(dataID) ? ttnDataSpecialFormat(dataID, dataValue) : ttnDataFormat(dataValue);

    if (checkDataIdIsMeasureUpload(dataID)) {
      // if telemetry.
      decoded.messages.push({
        type: 'report_telemetry',
        measurementId: dataID,
        measurementValue: realDataValue,
      });
    } else if (isSpecialDataId(dataID) || dataID === 5 || dataID === 6) {
      // if special order, except "report_sensor_id".
      switch (dataID) {
        case 0x00:
          // node version
          var versionData = sensorAttrForVersion(realDataValue);
          decoded.messages.push({
            type: 'upload_version',
            hardwareVersion: versionData.ver_hardware,
            softwareVersion: versionData.ver_software,
          });
          break;
        case 1:
          // sensor version
          break;
        case 2:
          // sensor eui, low bytes
          sensorEuiLowBytes = realDataValue;
          break;
        case 3:
          // sensor eui, high bytes
          sensorEuiHighBytes = realDataValue;
          break;
        case 7:
          // battery power && interval
          decoded.messages.push(
            {
              type: 'upload_battery',
              battery: realDataValue.power,
            },
            {
              type: 'upload_interval',
              interval: parseInt(realDataValue.interval) * 60,
            }
          );
          data.battery_perc = realDataValue.power;
          break;
        case 0x120:
          // remove sensor
          decoded.messages.push({
            type: 'report_remove_sensor',
            channel: 1,
          });
          break;
        default:
          break;
      }
    } else {
      decoded.messages.push({
        type: 'unknown_message',
        dataID: dataID,
        dataValue: dataValue,
      });
    }
  }

  // if the complete id received, as "upload_sensor_id"
  if (sensorEuiHighBytes && sensorEuiLowBytes) {
    decoded.messages.unshift({
      type: 'upload_sensor_id',
      channel: 1,
      sensorId: (sensorEuiHighBytes + sensorEuiLowBytes).toUpperCase(),
    });
  }
  
for (let i = 0; i < decoded.messages.length; i++) {
  // Check if the current message has a measurementId equal to 4099
  if (decoded.messages[i].measurementId === 4099) {
    data.brgt_lux = decoded.messages[i].measurementValue;
    break; // Break the loop once the value is found
  }
}
  
  // return
  return { data: data };
}

function crc16Check(data) {
  return true;
}

// util
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

// util
function divideBy7Bytes(str) {
  var frameArray = [];
  for (var i = 0; i < str.length - 4; i += 14) {
    var data = str.substring(i, i + 14);
    frameArray.push(data);
  }
  return frameArray;
}

// util
function littleEndianTransform(data) {
  var dataArray = [];
  for (var i = 0; i < data.length; i += 2) {
    dataArray.push(data.substring(i, i + 2));
  }
  dataArray.reverse();
  return dataArray;
}

// util
function strTo10SysNub(str) {
  var arr = littleEndianTransform(str);
  return parseInt(arr.toString().replace(/,/g, ''), 16);
}

// util
function checkDataIdIsMeasureUpload(dataId) {
  return parseInt(dataId) > 4096;
}

// configurable.
function isSpecialDataId(dataID) {
  switch (dataID) {
    case 0:
    case 1:
    case 2:
    case 3:
    case 4:
    case 7:
    case 0x120:
      return true;
    default:
      return false;
  }
}

// configurable
function ttnDataSpecialFormat(dataId, str) {
  var strReverse = littleEndianTransform(str);
  if (dataId === 2 || dataId === 3) {
    return strReverse.join('');
  }

  // handle unsigned number
  var str2 = toBinary(strReverse);

  var dataArray = [];
  switch (dataId) {
    case 0: // DATA_BOARD_VERSION
    case 1: // DATA_SENSOR_VERSION
      // Using point segmentation
      for (var k = 0; k < str2.length; k += 16) {
        var tmp146 = str2.substring(k, k + 16);
        tmp146 = (parseInt(tmp146.substring(0, 8), 2) || 0) + '.' + (parseInt(tmp146.substring(8, 16), 2) || 0);
        dataArray.push(tmp146);
      }
      return dataArray.join(',');
    case 4:
      for (var i = 0; i < str2.length; i += 8) {
        var item = parseInt(str2.substring(i, i + 8), 2);
        if (item < 10) {
          item = '0' + item.toString();
        } else {
          item = item.toString();
        }
        dataArray.push(item);
      }
      return dataArray.join('');
    case 7:
      // battery && interval
      return {
        interval: parseInt(str2.substr(0, 16), 2),
        power: parseInt(str2.substr(-16, 16), 2),
      };
  }
}

// util
function ttnDataFormat(str) {
  var strReverse = littleEndianTransform(str);
  var str2 = toBinary(strReverse);
  if (str2.substring(0, 1) === '1') {
    var arr = str2.split('');
    var reverseArr = [];
    for (var forArr = 0; forArr < arr.length; forArr++) {
      var item = arr[forArr];
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

// util
function sensorAttrForVersion(dataValue) {
  var dataValueSplitArray = dataValue.split(',');
  return {
    ver_hardware: dataValueSplitArray[0],
    ver_software: dataValueSplitArray[1],
  };
}

// util
function toBinary(arr) {
  var binaryData = [];
  for (var forArr = 0; forArr < arr.length; forArr++) {
    var item = arr[forArr];
    var data = parseInt(item, 16).toString(2);
    var dataLength = data.length;
    if (data.length !== 8) {
      for (var i = 0; i < 8 - dataLength; i++) {
        data = '0' + data;
      }
    }
    binaryData.push(data);
  }
  return binaryData.toString().replace(/,/g, '');
}

// Samples
// var sample = Decoder(["00", "00", "00", "01", "01", "00", "01", "00", "07", "00", "64", "00", "3C", "00", "01", "20", "01", "00", "00", "00", "00", "28", "90"], null);
// var sample = Decoder(["01", "01", "10", "98", "53", "00", "00", "01", "02", "10", "A8", "7A", "00", "00", "AF", "51"], null);
// var sample = Decoder(["01", "01", "00", "01", "01", "00", "01", "01", "02", "00", "6A", "01", "00", "15", "01", "03", "00", "30", "F1", "F7", "2C", "01", "04", "00", "09", "0C", "13", "14", "01", "05", "00", "7F", "4D", "00", "00", "01", "06", "00", "00", "00", "00", "00", "4C", "BE"], null);
// console.log(sample);

```