---
layout: default
title: Dragino - LHT65N-E5
parent: Sensors
---

<img src="https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/dragino-lht65n-e5_01.jpg" width="250" align="right">

# Dragino - LHT65N-E5
{: .no_toc }

- Manufacturer: <a href="https://www.dragino.com/" target="_blank">Dragino</a>
- Product: <a href="https://www.dragino.com/products/temperature-humidity-sensor/item/246-lht65n-e5-lorawan-temperature-humidity-illuminance-sensor.html" target="_blank">LHT65N-E5</a>

The LHT65N-E5 includes a built-in SHT20 temperature/humidity sensor and a jack to connect an external sensor e.g. a BH1750 based brightness/illumination sensor.

---

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Specifications
- indoor device (external sensor placement possible)
- Price ca. CHF 50.- (27.09.2024)
- Built-in sensors
  - <b>Temperature</b>, -40 ... +80 [°C], ± 0.3 °C
  - <b>relative Humidity</b>, 0 ... 99.9 [%rH], ± 3 %rH
- External sensor
  - <b>Brightness</b>, 0 ... 65'535 [lux], ± 1 lux
- Power Supply: 1 battery, 3.0 V, 2400 mAh (CR17450), Li-MnO₂
  - Expected life time: 8 ... 10 years at roomtemperature
- Min voltage: 2.45 V
- Size: 43 × 93 × 28 mm
- Weight: 105 g

---
## Documents
  - [Payload description v1.7 (2020-07-15)](https://www.dragino.com/downloads/downloads/LHT65/payload_decode/ttn_payload_decode_v1.7.txt)
  - [Datasheet from dragino.com (2024-09-27)](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/dragino-lht65n-e5_02.pdf)
  - [User manual from dragino.com (2024-09-27)](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/dragino-lht65n-e5_03.pdf)

---

## Ordering Info
- Part Number: LHT65N-XXX-YY
  - XXX is frequency band
  - YY is external sensor
- Version with external temperature sensor: <b>LHT65N-EU868-E3</b>
- [Ordering Link](https://www.bastelgarage.ch/lht65n-e5-lorawan-temperatur-und-humidity-sensor-mit-lichtfuhler?search=lht65n)

---

### Device Configuration
- Now you can see the incoming telegrams in the tab Data, but their content, the payload, is cryptic...!<br>
- We need to tell the "The Things Network" where to find e.g. the temperature etc. in these cryptic numbers and letters. We can do that with configuring a "Payload Decoder Function".

1. [Log in](https://console.thethingsnetwork.org/applications) and open the `application`
2. Select the tab `Payload Formats > decoder` and copy/paste the following code:

```javascript
function str1(str2) {
    let str3 = "";
    for (let i = 0; i < str2.length; i++) {
        if (str2[i] <= 0x0F) {
            str2[i] = "0" + str2[i].toString(16);
        }
        str3 += str2[i].toString(16);
    }
    return str3;
}

function strPad(byte) {
    const zero = '00';
    const hex = byte.toString(16);
    const tmp = 2 - hex.length;
    return zero.substr(0, tmp) + hex + " ";
}

function dataLog(i, bytes) {
    let ext = bytes[6] & 0x0F;
    let bb;
    if (ext === 1 || ext === 9 || ext === 2 || ext === 11) {
        bb = parseFloat(((bytes[0 + i] << 24 >> 16 | bytes[1 + i]) / 100).toFixed(2));
    } else if (ext === 4) {
        let extiPinLevel = bytes[0 + i] ? "High" : "Low";
        let extiStatus = bytes[1 + i] ? "True" : "False";
        bb = extiPinLevel + extiStatus;
    } else if (ext === 5 || ext === 7 || ext === 8 || ext === 14) {
        bb = bytes[0 + i] << 8 | bytes[1 + i];
    } else if (ext === 6) {
        bb = (bytes[0 + i] << 8 | bytes[1 + i]) / 1000;
    }
    let cc = parseFloat(((bytes[2 + i] << 24 >> 16 | bytes[3 + i]) / 100).toFixed(2));
    let dd = parseFloat((((bytes[4 + i] << 8 | bytes[5 + i]) & 0xFFF) / 10).toFixed(1));
    let ee = getMyDate((bytes[7 + i] << 24 | bytes[8 + i] << 16 | bytes[9 + i] << 8 | bytes[10 + i]).toString(10));
    let string = '[' + bb + ',' + cc + ',' + dd + ',' + ee + ']' + ',';
    return string;
}

function getzf(num) {
    if (parseInt(num) < 10) {
        num = '0' + num;
    }
    return num;
}

function getMyDate(str) {
    let date;
    if (str > 9999999999) {
        date = new Date(parseInt(str));
    } else {
        date = new Date(parseInt(str) * 1000);
    }
    let year = date.getFullYear();
    let month = date.getMonth() + 1;
    let day = date.getDate();
    let hour = date.getHours();
    let min = date.getMinutes();
    let sec = date.getSeconds();
    return year + '-' + getzf(month) + '-' + getzf(day) + ' ' + getzf(hour) + ':' + getzf(min) + ':' + getzf(sec);
}

function Decoder(bytes, port) {
    let ext = bytes[6] & 0x0F;
    let pollMessageStatus = (bytes[6] >> 6) & 0x01;
    let retransmissionStatus = (bytes[6] >> 7) & 0x01;
    let connect = (bytes[6] & 0x80) >> 7;
    let decode = {};
    let data = {};
    if (port === 3 && (bytes[2] === 0x01 || bytes[2] === 0x02 || bytes[2] === 0x03 || bytes[2] === 0x04)) {
        let array1 = [];
        let bytes1 = "0x";
        let str1 = str1(bytes);
        let str2 = str1.substring(0, 6);
        let str3 = str1.substring(6);
        let reg = /.{4}/g;
        let rs = str3.match(reg);
        rs.push(str3.substring(rs.join('').length));
        rs.pop();
        let newArr = [...rs];
        let data1 = newArr;
        decode.bat = parseInt(bytes1 + str2.substring(0, 4) & 0x3FFF);
        switch (parseInt(bytes1 + str2.substring(4))) {
            case 1:
                decode.sensor = "ds18b20";
                break;
            case 2:
                decode.sensor = "tmp117";
                break;
            case 3:
                decode.sensor = "gxht30";
                break;
            case 4:
                decode.sensor = "sht31";
                break;
        }
        for (let i = 0; i < data1.length; i++) {
            let temp = (parseInt(bytes1 + data1[i].substring(0, 4))) / 100;
            array1[i] = temp;
        }
        decode.Temp = array1;
        return decode;
    } else if (port === 5) {
        let sensor;
        switch (bytes[0]) {
            case 0x0B:
                sensor = "LHT65N";
                break;
            case 0x1A:
                sensor = "LHT65N-PIR";
                break;
        }
        subBand = bytes[4] === 0xff ? "NULL" : bytes[4];
        switch (bytes[3]) {
            case 0x01:
                freqBand = "EU868";
                break;
            case 0x02:
                freqBand = "US915";
                break;
            // Add cases for other frequency bands as necessary
        }
        let firmVer = (bytes[1] & 0x0f) + '.' + (bytes[2] >> 4 & 0x0f) + '.' + (bytes[2] & 0x0f);
        let bat = (bytes[5] << 8 | bytes[6]) / 1000;
        return {
            SENSOR_MODEL: sensor,
            FIRMWARE_VERSION: firmVer,
            BAT: bat,
        };
    }
    if (retransmissionStatus === 0) {
        switch (pollMessageStatus) {
            case 0:
                if (ext === 0x09) {
                    decode["temperature_degrC@external"] = parseFloat(((bytes[0] << 24 >> 16 | bytes[1]) / 100).toFixed(2));
                    decode.battery_state = bytes[4] >> 6;
                } else {
                    decode.battery_volt = ((bytes[0] << 8 | bytes[1]) & 0x3FFF) / 1000;
                    decode.battery_state = bytes[0] >> 6;
                }
                if (ext !== 0x0f) {
                    decode["temperature_degrC@internal"] = parseFloat(((bytes[2] << 24 >> 16 | bytes[3]) / 100).toFixed(2));
                    decode["humidity_perc@internal"] = parseFloat((((bytes[4] << 8 | bytes[5]) & 0xFFF) / 10).toFixed(1));
                }
                if (ext === 1 || ext === 2) {
									if ((bytes[7] !== 0xFF) && (bytes[8] !== 0xFF)) {
										let value = parseFloat(((bytes[7] << 24 >> 16 | bytes[8]) / 100).toFixed(2));
                    decode["temperature_degrC@external"] = value;
									}
                } else if (ext === 5) {	
									if ((bytes[7] !== 0xFF) && (bytes[8] !== 0xFF)) {
										let value = bytes[7] << 8 | bytes[8];
									  decode["brightness_lux@external"] = value;
									}
                } else if (ext === 11) {
                    decode["temperature_degrC@external"] = parseFloat(((bytes[7] << 24 >> 16 | bytes[8]) / 100).toFixed(2));
                    decode["humidity_perc@external"] = parseFloat((((bytes[9] << 8 | bytes[10]) & 0xFFF) / 10).toFixed(1));
                }
                if ((bytes.length === 11) || (bytes.length === 15)) {
                    return decode;
                }
                break;
            default:
                return {
                    errors: ["unknown"]
                };
        }
    } else {
        switch (retransmissionStatus) {
            case 1:
                for (let i = 0; i < bytes.length; i = i + 11) {
                    let da = dataLog(i, bytes);
                    if (i === 0)
                        data.retransmission_message = da;
                    else
                        data.retransmission_message += da;
                }
                return data;
            default:
                return {
                    errors: ["unknown"]
                };
        }
    }
}
```

3. Copy/Paste the following test payload into the field `Payload` and press `Test`
```
CC 15 09 8C 02 7B 05 00 06 7F FF
```
4. You should see the following result
```json
{
  "battery_state": 3,
  "battery_volt": 3.093,
  "brightness_lux@external": 6,
  "humidity_perc@internal": 63.5,
  "temperature_degrC@internal": 24.44
}
```
- <b>BatV</b> -> battery voltage [V]<br>
- <b>Ext_sensor</b> -> connected external sensor<br>
- <b>Hum_SHT</b> -> Humidity internal sensor [%rH]<br>
- <b>TempC_DS</b> -> Temperature external sensor [°C]<br>
- <b>TempC_SHT</b> -> Temperature internal sensor [°C]<br>

5. Press `save payload functions`

- Now you should be able to see the decoded data of your sensor in the tab `Data`.<br>
- Trigger a new telegram by pressing the ACT-button on the dragino LHT65 for a short time (> 1s and < 3s).<br><br>
- The dragino LHT65 sends a telegram once every 10 minutes.<br>

---

## Optional Settings

### Change sampling interval
To change the sampling interval, you have to send the device configuration telegrams (Downlink-Messages)
The time interval in minutes at which the sensor queries the current values.

1. In the TTN Console on the device view, select the device and change to the tab `Messaging`, select `Downlink`
2. Change the `FPort to 2`
3. Copy/paste the payload, e.g. `0100012C` into the `Payload` field to set interval to 5 minutes
4. Press `Send`
5. In the `Data` tab you should now see the scheduled telegram. The wisely sensor only receives downlink data after a transmission. Therefore start a transmission by pressing the button on the back of the sensor (push once short, green led will illuminate)

#### Examples
'0100' is an identifier, the rest represents the sampling interval in hex

-	5 Minutes Interval:  '0100**012C**' (300s in hex are '012C')
-	15 Minutes Interval: '0100**0384**' (900s in hex are '0384')
-	60 Minutes Interval: '0100**0E10**' (3600s in hex are '0E10')