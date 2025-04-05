---
layout: default
title: Avelon - Wisely AllSense
parent: Sensors
---

<img src="https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/avelon-wisely-allsense_01.png" width="250" align="right" class="inline"/>

# Avelon - Wisely AllSense
{: .no_toc }

- Manufacturer: <a href="https://avelon.com/" target="_blank">Avelon</a>
- Product: <a href="https://avelon.com/en/wisely/" target="_blank">Wisely AllSense</a>

The Wisely AllSense is an indoor room sensor to measure temperature, humidity, CO<sub>2</sub>, VOC, brightness, occupancy and atmosperic pressure.

---
## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Specifications
- Indoor device
- Price ca. CHF 236.- (03.08.2023)
- Sensors
  - **Temperature**, -10 ... +80 [°C], ± 0.2 °C between 20 ... 65 °C
  - **relative Humidity**, 0 ... 95[%rH], ± 1.5 %rH between 10...80 %rH
  - **CO<sub>2</sub>**, 400 ... 5'000 [ppm], ± 30 ppm + 3 % of measurement value, NDIR, ABC Correction
  - **VOC**, IAQ 0-500 [-], ±15 % ±1
  - **Occupancy**, 0...60 [min], detection range up to 5 meters
  - **Brightness**, 1-32'000 [lux]
  - **atmospheric pressure**, [hPa], ± 1 hPa
- Power Supply: 1 battery, 3.6 V, 3600 mAh, (A), Li-SOCl₂
  - Expected life time: 3 ... 5 years at roomtemperature, depends on payload size and settings
- Size: 80 × 80 × 27 mm
- Weight: 100 g

---

## Documents / Links
- [Payload description from avelon](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/avelon-wisely-payload.pdf)
- [Wisely Datasheet from avelon.com (2020-07-14)](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/avelon-wisely-carbonsense_03.pdf)
- [FAQ on avelon.com](https://avelon.com/support/wisely-faq/)
- [Temp- and Humidity Sensor Datasheet - Sensirion SHT3x](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/avelon-wisely-sensirion-sht3x.pdf)
- [Atmospheric Pressure Sensor Datasheet - Bosch BMP380](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/avelon-wisely-bosch-bmp280.pdf)
- [CO<sub>2</sub> Sensor Datasheet - Senseair Sunrise](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/avelon-wisely-senseair-sunrise.pdf)
- [VOC Sensor Datasheet - Bosch BME680](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/avelon-wisely-bosch-bme680.pdf)
- [Wisely LED Payload Creator Excel (2023-10-19)](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/avelon-wisely-ledPayload.xlsx)

---

## Wisely Product Overview

| Sensor               | Wisely Standard | Wisely CarbonSense | Wisely AllSense (Simple Payload) | Wisely AllSense (Extended Payload) |
|----------------------|:---------------:|:------------------:|:--------------------------------:|:----------------------------------:|
| Atmospheric pressure |        ✓        |          ✓         |                 *                |                 ✓ *                |
| Temperature          |        ✓        |          ✓         |                 ✓                |                  ✓                 |
| Humidity             |        ✓        |          ✓         |                 ✓                |                  ✓                 |
| VOC                  |                 |                    |                 ✓                |                  ✓                 |
| Brightness           |                 |                    |                 *                |                 ✓ *                |
| CO<sub>2</sub>       |                 |          ✓         |                 ✓                |                  ✓                 |
| PIR/Presence         |                 |                    |                 *                |                 ✓ *                |

* Due to limitations in payload length, these sensors are not activated by default, see chapter "Optional Settings" to change that.  

---

## Ordering Info
Attention, there are four different versions of the same sensor which have different software loaded on them. Only the "Self-Managed" version works with the lcm application. Do not order the version "The Things Network", it will not work with the lcm application.

- **Self-Managed -> that's what you want to have!!!**<br>
  Data goes over the ttn network to the ttn server where the lcm application can fetch them, no monthly charges or fees.

**Other variants which don't work with TTN**
- **Swisscom LPN**<br>
  Means that the data goes to the AVELON server via Swisscom low power network, monthly charges apply.
- **The Things Network**<br>
  Means that the data goes via ttn, but to the AVELON-server! No monthly charges. In this case the lcm application can't access the data!
- **Building Automation**<br>
  Integration in the Avelon automation system, license fees system.

[Ordering Link](https://avelon.com/en/buy-wisely/)

---

## Device specific Information

### LED blinking behaviour
If a CO<sub>2</sub> limit is exceeded, the LED on the front side of the device changes color. It blinks every [SensorSampleTime](https://hslu-ige-laes.github.io/lora-devices-ttn/docs/sensors/avelon-wisely-allsense/#sensorsampletime) (250 ms lighting, 500 ms pause - repeating 4 times).

| CO<sub>2</sub> value | LED Color |
|----------------------|-----------|
| 0 ... 800              | green (on some devices this is deactivated)  |
| 801 ... 1400           | blue      |
| > 1400               | red       |

To deactivate or change the led behaviour see [Change the LED blinking behaviour](https://hslu-ige-laes.github.io/lora-devices-ttn/docs/sensors/avelon-wisely-allsense/#change-the-led-blinking-behaviour)

### IAQ - Indoor Air Quality Index (VOC Sensor)

| IAQ| Description |
|-----------|--------------------------|
| 0 ... 50 | excellent |
| 51 ... 100 | good |
| 101 ... 150 | lightly polluted |
| 151 ... 200 | moderately polluted |
| 201 ... 250 | heavily polluted |
| 251 ... 350 | severely polluted |
| > 350 | extremely polluted |

### CO<sub>2</sub> Sensor ABC Algorithm
The ABC algorithm in CO<sub>2</sub> sensors uses automatic base correction to reduce fluctuations and drifts in CO<sub>2</sub> measurements.
It continuously determines the zero point of the CO<sub>2</sub> signal and adjusts it to 400 ppm (more or less the outside level) to ensure accurate and stable detection of CO<sub>2</sub> concentrations in ambient air.
This improves the long-term stability and reliability of CO<sub>2</sub> sensors.

However, this means that the sensor must be exposed to clean air at least every 14 days. Otherwise the CO<sub>2</sub> value will drift.

The sensor determines this base value during the first 2 weeks of operation and only provides usable values after this time.
It is therefore important that the sensor has been exposed to the outside air for more than 2 hours during this time.
If you want to accelerate this process, you can expose the sensor to the outside air and then send a downlink payload `B2`.
This is how you end the 2 weeks period.

1. In the TTN Console on the device view, select the device and change to the tab `Messaging`, select `Downlink`
2. Change the `FPort to 10`
3. Write `B2` into the `Payload` field
4. Press `Send`
5. In the `Data` tab you should now see the scheduled telegram. The wisely sensor only receives downlink data after a transmission. Therefore start a transmission by pressing the button on the back of the sensor (push once short, green led will illuminate)

---

## Handler Change
- The Wisely sensors are per default configured for the Avelon Cloud, even if ordered as "self-managed". Thats why we have to detach the device from the avelon cloud.

1. Switch the device on and scan the `QR-Code` with e.g. a Mobile Phone.
2. Click on the round `information (i) symbol` top right aside the sensor name
3. Press `Register for free`
4. Press `create new account` or login if you already have created an account
5. When promted select `Private` and fill in the fields, then register
6. Assign the device and skip the `select location` as well as `alerting`
7. Change to your computer and log in to [https://avelon.cloud/login](https://avelon.cloud/login)
8. Click `your name > Devices` on top right
9. Your device should appear. Click on it to open the `Device Configuration`
10. Click on the `three small circles top right (More)` and choose `Configure LoraWAN Network Provider`
11. Choose `Self-Managed` and hit `OK`
12. Press the `blue floppy disk symbol` top right to save the configuration.
12. Copy the `Device EUI`, `Application EUI` and `Application Key`, we will need them later on.
13. Now we have to `reset the device manually` approximately 1-2 minutes after closing the previous dialog by pressing the small button on the back of the device. `5 seconds -> Off` , `2 seconds -> On`

---

## Adding the Device to TTN
- Before a device can communicate via "The Things Network" we need to register it with an application.<br>
- The Avelon Wisely sensors use the so called "Over The Air Activation" (OTAA) and for a secure communication we will need to register the beforehand copied keys.

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
10. Now we have to reset the device manually by pressing the small button on the back of the device. `5 seconds -> Off` , `2 seconds -> On`

- Now the device should join the network and you can see the incoming telegrams in the `Live data` section
  - if not, please wait several hours and check again. The change of the handler can take a long time...

---

## Optional Settings

### Change sensor selection (Payload format)
Per default the Wisely AllSense is transmitting a reduced set of measurements, the so called "Simple Payload".
Only with the "Extended Payload", the Wisely AllSense is transmitting the Pressure, Brighntess and Presence information.

| Sensor               | Simple Payload | Extended Payload |
|----------------------|:--------------:|:----------------:|
| Atmospheric pressure |                |         ✓        |
| Temperature          |        ✓       |         ✓        |
| Humidity             |        ✓       |         ✓        |
| VOC                  |        ✓       |         ✓        |
| Brightness           |                |         ✓        |
| CO<sub>2</sub>       |        ✓       |         ✓        |
| PIR/Presence         |                |         ✓        |

To change the payload type the device has to be configured accordingly:

1. Select the device and change to the tab `Messaging`, select `Downlink`
2. Change the `FPort to 10`
3. Copy/paste `FF FE 02` into the `Payload` field for changing to the "Extended payload"
4. Press `Send`

To change the format back to "Simple Payload", send `FF FE 01`

### Change sampling and transmission intervals
There are two configuration values, `SensorSampleTime` and `CyclicTransmissionCounter`. 
To change these two values, you have to send the device configuration telegrams (Downlink-Messages)

#### SensorSampleTime
The time interval in minutes at which the sensor queries the current values.

1. In the TTN Console on the device view, select the device and change to the tab `Messaging`, select `Downlink`
2. Change the `FPort to 10`
3. Copy/paste the payload from the examples below, e.g. `FF 01 0A` into the `Payload` field
4. Press `Send`
5. In the `Data` tab you should now see the scheduled telegram. The wisely sensor only receives downlink data after a transmission. Therefore start a transmission by pressing the button on the back of the sensor (push once short, green led will illuminate)

#### CyclicTransmissionTime and CyclicTransmissionCounter
The time interval at which the sensor transmits the recorded measurement values. Must be a multiple of the sampling period.

To avoid overburdening the battery of the device, the smallest permissible time interval is 10 minutes.

The CyclicTransmissionTime cannot be configured. Instead, a counter can be configured.

`CyclicTransmissionTime = CyclicTransmissionCounter × SensorSampleTime`

This setting cannot be configured. But instead the value `CyclicTransmissionCounter` can be set.

#### CyclicTransmissionCounter
Per default the CyclicTransmissionCounter is set to 60, which means the device in default configuration sends data every 60 minutes (60 × 1 min = 60 min).

The device sends the data cyclically. When a certain number of samples has been taken, the measured values are sent.
The `CyclicTransmissionCounter` is therefore the number of samples that are to be sent together.

`CyclicTransmissionCounter = CyclicTransmissionTime / SensorSampleTime`

Per default the `CyclicTransmissionCounter` is set to 60, so every 1h a set of 6 measurements of all sensors gets transmitted.

1. In the TTN Console on the device view, select the device and change to the tab `Messaging`, select `Downlink`
2. Change the `FPort to 10`
3. Copy/paste the payload from the examples below, e.g. `FF F0 06` into the `Payload` field
4. Press `Send`
5. In the `Data` tab you should now see the scheduled telegram. The wisely sensor only receives downlink data after a transmission. Therefore start a transmission by pressing the button on the back of the sensor (push once short, green led will illuminate)

#### Example configurations
**Example 1 (default config)**<br>
This configuration makes sense if the LED is used, so the occupant gets every minute a flashing LED.
If the LED gets deactivated anyway, the sampling rate is unecessary high.

- SensorSampleTime = 1 (HEX 01 -> `FF 01 01`)
  Every 1 min a sensor sample, every 10 min an averaged measurement.
- CyclicTransmissionCounter = 60 (HEX 3C -> `FF F0 3C`)
  After 60 samples a data transmission, that means 6 averaged measurements in the payload, transmitted every 60 minutes.

**Example 2**<br>
This configuration makes sense if the LED is used and the values should arrive faster than every hour. This has an impact on battery life!
If the LED gets deactivated anyway, the sampling rate is unecessary high.

- SensorSampleTime = 1 (HEX 01 -> `FF 01 01`)
  Every 1 min a sensor sample, every 10 min an averaged measurement.
- CyclicTransmissionCounter = 20 (HEX 14 -> `FF F0 14`)
  After 20 samples a data transmission, that means 2 averaged measurements in the payload, transmitted every 20 minutes.

**Example 3**<br>
This configuration makes sense if the smallest possible measuring rate is required and the LED gets deactivated. 

- SensorSampleTime = 10 (HEX 0A -> `FF 01 0A`)
  Every 10 min a sensor sample, every 10 min an averaged measurement.
- CyclicTransmissionCounter = 6 (HEX 06 -> `FF F0 06`)
  After 6 samples a data transmission, that means 6 averaged measurements in the payload, transmitted every 60 minutes.

**Example 4**<br>
This configuration makes sense if a long battery life is required and the LED gets deactivated.
The sensor acts as a logger and the data is not real time, but battery life is optimized. Small changes in sensor values cannot get detected, so the accuracy is slightly lower. E.g. the impact of an opened window might not be visible in the temperature data.

- SensorSampleTime = 60 (HEX 3C -> `FF 01 3C`)
  Every 60 min a sensor sample, every 60 min an averaged measurement.
- CyclicTransmissionCounter = 24 (HEX 18 -> `FF F0 18`)
  After 6 samples a data transmission, that means 6 averaged measurements in the payload, transmitted every 360 minutes (6 hours).

### Change the LED blinking behaviour
With the following procedure you can deactivate the LED

1. Select the device and change to the tab `Messaging`, select `Downlink`
2. Change the `FPort to 10`
3. Copy/paste `06 06 0B 03 20 00 00 FF 00` into the `Payload` field for deactivating the blue led
4. Press `Send`
5. The wisely sensor only receives downlink data after a transmission. Therefore start a transmission by pressing the button on the back of the sensor (push once short, green led will illuminate)
6. Copy/paste `06 06 0B 05 78 FF 00 00 00` into the `Payload` field for deactivating the red led
7. Press `Send`
8. Press again the button on the back of the sensor

- Now the led should not blink anymore.
- To reset the behaviour exchange the last byte with `01` instead of `00`:
  - `06 06 0B 03 20 00 00 FF 01`
  - `06 06 0B 05 78 FF 00 00 01`

Look at the [Wisely LED Payload Creator Excel](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/avelon-wisely-ledPayload.xlsx) to create the payload information.

Further info in the [payload description ](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/avelon-wisely-payload.pdf).

---

## Payload formatter

```javascript
function getValues(bytes, measurement, byteIndices, deviceType, datasetCount, datasetLength, payloadOffset) {
  var decoded = [];
  var measurementByteLengths = {"pressure_hPa": 2, "temperature_degrC": 2, "humidity_perc": 1, "voc_index": 2, "brightness_lux": 2, "co2_ppm": 2, "presence_min": 2};
  var divFactors = {"pressure_hPa": 10.0, "temperature_degrC": 10.0, "humidity_perc": 2.0, "voc_index": 1.0, "brightness_lux": 1.0, "co2_ppm": 1.0, "presence_min": 1.0};

  if (measurement in byteIndices[deviceType]) {
    var byteIndexValue = byteIndices[deviceType][measurement];

    for (var i = 0; i < datasetCount; i++) {
      var byteIndex = i * datasetLength + byteIndexValue + payloadOffset;
      if (measurementByteLengths[measurement] === 2) {
        decoded.push((bytes[byteIndex] << 8 | bytes[byteIndex + 1]) / divFactors[measurement]);
      } else {
        decoded.push((bytes[byteIndex]) / divFactors[measurement]);
      }
    }
  }
  return decoded;
}

function decodeUplink(input) {
  var deviceType = "AllSense";
  var samplingRate = 10; //minutes
  var datasetLengthDict = {
    "Standard": 5,
    "CarbonSense": 7,
    "AllSense": 7,
    "AllSenseExt": 13
  };
  var byteIndices = {
    "Standard": {"pressure_hPa": 0, "temperature_degrC": 2, "humidity_perc": 4},
    "CarbonSense": {"pressure_hPa": 0, "temperature_degrC": 2, "humidity_perc": 4, "co2_ppm": 5},
    "AllSense": {"temperature_degrC": 0, "humidity_perc": 2, "voc_index": 3, "co2_ppm": 5},
    "AllSenseExt": {"pressure_hPa": 0, "temperature_degrC": 2, "humidity_perc": 4, "voc_index": 5, "brightness_lux": 7, "co2_ppm": 9, "presence_min": 11}
  };
  var payloadOffset = 1; // Offset of battery information, offset before datasets
  var warnings = [];
  var errors = [];
  
  if(datasetLengthDict[deviceType] === undefined){
    errors.push("Error: Typo in deviceType configuration in payload decoder");
  }
  
  if((input.fPort === 5) || (input.fPort === 6)){
    // Battery status and percent
    var batVal = input.bytes[0];
    var batteryPerc;
    if((batVal >= 30) && (batVal < 254)){
      batteryPerc = Math.round(batVal / 254.0 * 100.0 * 10) / 10;
    }else if((batVal < 30) && (batVal > 1)){
      batteryPerc = Math.round(batVal / 254.0 * 100.0 * 10) / 10;
      warnings.push("Battery Warning: Low state");
    }else if((batVal === 1)){
      batteryPerc = 1;
      warnings.push("Battery Warning: No further battery capacity available");
    }else if(batVal === 254){
      batteryPerc = 100.0;
    } else{
      batteryPerc = 0.0;
      warnings.push("Battery Warning: Could not acquire the voltage");
    }
      
    if(input.fPort === 6 && deviceType !== "AllSense"){
      warnings.push("Warning: deviceType in payload decoder is not set to AllSense");
    }
    
    var datasetLength = datasetLengthDict[deviceType];
    var datasetCount = (input.bytes.length - 2) / datasetLength;
    var readings = [];
    
    if(!Number.isInteger(datasetCount)) {
      errors.push("Error: datasetCount is not a whole number!");
    } else {
      for (var i = 0; i < datasetCount; i++) {
        var reading = { battery_perc: batteryPerc };
        
        for (const [key, value] of Object.entries(byteIndices[deviceType])) {
          var decoded = getValues(input.bytes, key, byteIndices, deviceType, 1, datasetLength, payloadOffset + i * datasetLength);
          if (decoded.length > 0) {
            reading[key] = decoded[0];
          }
        }
        reading["offset"] = (datasetCount - i - 1) * samplingRate * (-1); 
        readings.push(reading);
      }
    }
  } else {
    errors.push("Unknown fPort");
  }

  return {
    data: readings,
    warnings: warnings,
    errors: errors
  };
}
```
