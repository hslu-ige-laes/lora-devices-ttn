---
layout: default
title: Avelon - Wisely CarbonSense
parent: Sensors
---

<img src="https://hslu-ige-laes.github.io/lora-devices-ttn/docs/sensors/avelon-wisely-carbonsense_01.png" width="250" align="right" class="inline"/>

# Avelon - Wisely CarbonSense
{: .no_toc }

- Manufacturer: <a href="https://avelon.com/" target="_blank">Avelon</a>
- Product: <a href="https://avelon.com/en/wisely/" target="_blank">Wisely CarbonSense</a>

The Wisely CarbonSense is an indoor room sensor to measure temperature, humidity, CO<sub>2</sub> and atmosperic pressure.

---
## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Specifications
- Indoor device
- Price ca. CHF 191.- (25.05.2022)
- Sensors
  - **Temperature**, -10 ... +80 [°C], ± 0.1 °C between 20 ... 60 °C
  - **relative Humidity**, 0 ... 95[%rH], ± 1.5 %rH between 10...80 %rH
  - **CO<sub>2</sub>**, 400 ... 5'000 [ppm], ± 30 ppm + 3 % of measurement value
  - **atmospheric pressure**, [hPa], ± 1 hPa
    Attention: A series of Wisely CarbonSense (software version < 3.3) does not support air pressure measurement!
- Power Supply: 1 battery, 3.6 V, 3600 mAh, (A), Li-SOCl₂
  - Expected life time: 3 ... 8 years at roomtemperature
- Size: 80 × 80 × 27 mm
- Weight: 100 g

---

## Documents / Links
- [Payload description from avelon (online)](https://avelon.cloud/docs/de/appendix/wisely-payload.html)
- [Wisely Datasheet from avelon.com (2020-07-14)](https://hslu-ige-laes.github.io/lora-devices-ttn/docs/sensors/avelon-wisely-carbonsense_03.pdf)
- [FAQ on avelon.com](https://avelon.com/support/wisely-faq/)
- [Temp- and Humidity Sensor Datasheet - Sensirion SHT3x](https://hslu-ige-laes.github.io/lora-devices-ttn/docs/sensors/avelon-wisely-sensirion-sht3x.pdf)
- [Atmospheric Pressure Sensor Datasheet - Bosch BMP380](https://hslu-ige-laes.github.io/lora-devices-ttn/docs/sensors/avelon-wisely-bosch-bmp280.pdf)
- [CO<sub>2</sub> Sensor Datasheet - Senseair Sunrise](https://hslu-ige-laes.github.io/lora-devices-ttn/docs/sensors/avelon-wisely-senseair-sunrise.pdf)
- [Wisely LED Payload Creator Excel (2023-10-19)](https://hslu-ige-laes.github.io/lora-devices-ttn/docs/sensors/avelon-wisely-ledPayload.xlsx)

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

* Due to limitations in payload length, these sensors are not activated by default
  
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

## LED States

If a CO<sub>2</sub> limit is exceeded, the LED on the front side of the device changes color. According to the Avelon payload description it blinks every 60 seconds while the limit is exceeded (250 ms lighting, 500 ms pause - repeating 4 times).

The limit is evaluated on the measured CO<sub>2</sub> value. A new value is only available every [SensorSampleTime](https://hslu-ige-laes.github.io/lora-devices-ttn/docs/sensors/avelon-wisely-carbonsense/#sensorsampletime), so with a longer SensorSampleTime the LED reacts more slowly to changes in CO<sub>2</sub> concentration.

| CO<sub>2</sub> value | LED Color |
|----------------------|-----------|
| 0 ... 800              | green (on some devices this is deactivated)  |
| 801 ... 1400           | blue      |
| > 1400               | red       |

To deactivate or change the led behaviour see [Change the LED blinking behaviour](https://hslu-ige-laes.github.io/lora-devices-ttn/docs/sensors/avelon-wisely-carbonsense/#change-the-led-blinking-behaviour)

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

### Handler Change
- The Wisely sensors are per default configured for the Avelon Cloud, even if ordered as "self-managed". Thats why we have to detach the device from the avelon cloud.

1. Switch the device on and scan the `QR-Code` with e.g. a Mobile Phone ([detailed information here](https://avelon.com/wp-content/uploads/2020/03/wisely-quick-guide.pdf))
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

### Change sampling and transmission intervals
There are two configuration values, `SensorSampleTime` and `CyclicTransmissionCounter`. 
To change these two values, you have to send the device configuration telegrams (Downlink-Messages).

#### How sampling and transmission work
- **Every sample is one measurement.** Each time the sensor samples (every `SensorSampleTime`), one complete dataset (pressure, temperature, humidity, CO<sub>2</sub> = 7 bytes) is stored. The device does **not** average samples.
- After `CyclicTransmissionCounter` samples, the stored datasets are transmitted together in one payload (1 byte battery + n × 7 bytes datasets + 1 byte offset).
- A LoRaWAN telegram can only carry a limited number of bytes, depending on the spreading factor (EU868: 51 bytes at SF10–SF12, 115 bytes at SF9, 222 bytes at SF7–SF8). If the collected datasets don't fit, the device sends a full telegram **before** the scheduled transmission and the remaining datasets with the next one. Such early telegrams are marked with `OFFSET > 0` (last byte of the payload).

`Values per day = 1440 / SensorSampleTime`

`Bytes per transmission = 2 + CyclicTransmissionCounter × 7`

#### SensorSampleTime
The time interval in minutes between two measurements. Each sample results in one dataset in the payload.

According to Avelon, the default value is 1 minute (`FF 01 01`).

1. In the TTN Console on the device view, select the device and change to the tab `Messaging`, select `Downlink`
2. Change the `FPort to 10`
3. Copy/paste the payload from the examples below, e.g. `FF 01 0A` into the `Payload` field
4. Press `Send`
5. In the `Data` tab you should now see the scheduled telegram. The wisely sensor only receives downlink data after a transmission. Therefore start a transmission by pressing the button on the back of the sensor (push once short, green led will illuminate)

#### CyclicTransmissionCounter
The number of samples (datasets) that are collected before a transmission. The resulting transmission interval is:

`CyclicTransmissionTime = CyclicTransmissionCounter × SensorSampleTime`

The CyclicTransmissionTime itself cannot be configured, only the counter. To avoid overburdening the battery of the device, the smallest permissible transmission interval is 10 minutes.

According to Avelon, the default value is 60 (`FF F0 3C`). Together with the default SensorSampleTime of 1 minute this means **60 datasets per hour** (1440 values per day). These 420 bytes don't fit into one telegram, so the device sends several telegrams per hour (typically about every 10 minutes, depending on the spreading factor).

1. In the TTN Console on the device view, select the device and change to the tab `Messaging`, select `Downlink`
2. Change the `FPort to 10`
3. Copy/paste the payload from the examples below, e.g. `FF F0 06` into the `Payload` field
4. Press `Send`
5. In the `Data` tab you should now see the scheduled telegram. The wisely sensor only receives downlink data after a transmission. Therefore start a transmission by pressing the button on the back of the sensor (push once short, green led will illuminate)

**Important:** Only one downlink is delivered per uplink. Send the two configuration downlinks one after the other and check in the TTN Console that the first one has been delivered before scheduling the second one. Data already collected with the old settings may still be transmitted afterwards, so wait one or two transmission intervals before checking the result.

To check the actual configuration, look at the payload length of an uplink on port 5: `number of datasets = (payload bytes - 2) / 7`.

#### Example configurations
**Example 1 (default config)**<br>
Fastest LED reaction and highest time resolution, but highest battery consumption and airtime. If the LED is deactivated anyway, the sampling rate is unnecessarily high.

- SensorSampleTime = 1 (HEX 01 -> `FF 01 01`)
  Every 1 min one measurement.
- CyclicTransmissionCounter = 60 (HEX 3C -> `FF F0 3C`)
  60 datasets (420 bytes) per hour → split into several telegrams per hour, 1440 values per day.

**Example 2**<br>
Fast LED reaction, values arrive at least every 20 minutes. High impact on battery life!

- SensorSampleTime = 1 (HEX 01 -> `FF 01 01`)
  Every 1 min one measurement.
- CyclicTransmissionCounter = 20 (HEX 14 -> `FF F0 14`)
  20 datasets (142 bytes) every 20 minutes → fits into one telegram only at SF7/SF8, otherwise split. 1440 values per day.

**Example 3 (recommended for 10 min values)**<br>
10-minute resolution with one telegram per hour. The LED still works, but reacts with a delay of up to 10 minutes.

- SensorSampleTime = 10 (HEX 0A -> `FF 01 0A`)
  Every 10 min one measurement.
- CyclicTransmissionCounter = 6 (HEX 06 -> `FF F0 06`)
  6 datasets (44 bytes) transmitted every 60 minutes → fits into one telegram at all spreading factors. 144 values per day.

**Example 4**<br>
Long battery life, the sensor acts as a logger. The data is not real time and short events (e.g. an opened window) might not be visible. The LED should be deactivated, as it only reacts once per hour.

- SensorSampleTime = 60 (HEX 3C -> `FF 01 3C`)
  Every 60 min one measurement.
- CyclicTransmissionCounter = 6 (HEX 06 -> `FF F0 06`)
  6 datasets (44 bytes) transmitted every 360 minutes (6 hours). 24 values per day.

Don't forget to set `SENSOR_SAMPLE_TIME_MIN` and `CYCLIC_TRANSMISSION_COUNTER` in the [payload decoder](https://hslu-ige-laes.github.io/lora-devices-ttn/docs/sensors/avelon-wisely-carbonsense/#payload-decoder) to the same values, otherwise the timestamps of the datasets are wrong.

### Change the led blinking behaviour
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

Look at the [Wisely LED Payload Creator Excel](https://hslu-ige-laes.github.io/lora-devices-ttn/docs/sensors/avelon-wisely-ledPayload.xlsx) to create the payload information.

Further info in the [payload description ](https://hslu-ige-laes.github.io/lora-devices-ttn/docs/sensors/avelon-wisely-payload.pdf).

---

## Payload Decoder

Set the three configuration values at the top to match the device. `SENSOR_SAMPLE_TIME_MIN` and `CYCLIC_TRANSMISSION_COUNTER` are needed to calculate the time offset of each dataset.

```javascript
// ===== Configuration - must match the settings of the device =====
var DEVICE_TYPE = "CarbonSense";     // "Standard", "CarbonSense" or "AllSense"
var SENSOR_SAMPLE_TIME_MIN = 10;     // SensorSampleTime in minutes (downlink FF 01 xx)
var CYCLIC_TRANSMISSION_COUNTER = 6; // CyclicTransmissionCounter (downlink FF F0 xx)
// =================================================================

var DATASET_LENGTH = { "Standard": 5, "CarbonSense": 7, "AllSense": 7, "AllSenseExt": 13 };

var BYTE_INDICES = {
  "Standard":    { "pressure_hPa_abs": 0, "temperature_degrC_abs": 2, "humidity_perc_abs": 4 },
  "CarbonSense": { "pressure_hPa_abs": 0, "temperature_degrC_abs": 2, "humidity_perc_abs": 4, "co2_ppm_abs": 5 },
  "AllSense":    { "temperature_degrC_abs": 0, "humidity_perc_abs": 2, "voc_index_abs": 3, "co2_ppm_abs": 5 },
  "AllSenseExt": { "pressure_hPa_abs": 0, "temperature_degrC_abs": 2, "humidity_perc_abs": 4, "voc_index_abs": 5, "brightness_lux_abs": 7, "co2_ppm_abs": 9, "presence_min_inc": 11 }
};

var BYTE_LENGTHS = { "pressure_hPa_abs": 2, "temperature_degrC_abs": 2, "humidity_perc_abs": 1, "voc_index_abs": 2, "brightness_lux_abs": 2, "co2_ppm_abs": 2, "presence_min_inc": 2 };
var DIV_FACTORS  = { "pressure_hPa_abs": 10.0, "temperature_degrC_abs": 10.0, "humidity_perc_abs": 2.0, "voc_index_abs": 1.0, "brightness_lux_abs": 1.0, "co2_ppm_abs": 1.0, "presence_min_inc": 1.0 };
var SIGNED       = { "temperature_degrC_abs": true }; // Int16

function mapBatteryLevelPerc(batteryPercent) {
  if (batteryPercent === null) {
    return null;
  } else if (batteryPercent <= 90) {
    return 0; // Critical
  } else if (batteryPercent <= 95) {
    return 1; // Warning
  } else if (batteryPercent <= 99.9) {
    return 2; // Good
  } else {
    return 3; // Very Good
  }
}

function decodeBattery(batVal, warnings) {
  if (batVal === 0) {
    warnings.push("No battery inserted (external power supply)");
    return null;
  }
  if (batVal === 255) {
    warnings.push("Battery Warning: Could not acquire the voltage");
    return null;
  }
  if (batVal === 1) {
    warnings.push("Battery Warning: No further battery capacity available");
  } else if (batVal < 30) {
    warnings.push("Battery Warning: Low state");
  }
  return Math.round(batVal / 254.0 * 100.0 * 10) / 10;
}

function readValue(bytes, index, key) {
  var value;
  if (BYTE_LENGTHS[key] === 2) {
    value = (bytes[index] << 8) | bytes[index + 1];
    if (SIGNED[key] && value > 0x7FFF) {
      value -= 0x10000;
    }
  } else {
    value = bytes[index];
  }
  return value / DIV_FACTORS[key];
}

function decodeUplink(input) {
  var deviceType = DEVICE_TYPE;
  var bytes = input.bytes;
  var readings = [];
  var warnings = [];
  var errors = [];

  if (DATASET_LENGTH[deviceType] === undefined) {
    errors.push("Error: Typo in DEVICE_TYPE configuration in payload decoder");
    return { data: readings, warnings: warnings, errors: errors };
  }

  if (input.fPort !== 5 && input.fPort !== 6) {
    errors.push("Unknown fPort " + input.fPort);
    return { data: readings, warnings: warnings, errors: errors };
  }

  if (input.fPort === 6) {
    if (deviceType !== "AllSense") {
      errors.push("Error: fPort 6 (extended payload) is only used by Wisely AllSense, check DEVICE_TYPE");
      return { data: readings, warnings: warnings, errors: errors };
    }
    deviceType = "AllSenseExt";
  }

  var datasetLength = DATASET_LENGTH[deviceType];
  var datasetCount = (bytes.length - 2) / datasetLength; // 1 byte battery + datasets + 1 byte offset

  if (bytes.length < 2 + datasetLength || datasetCount % 1 !== 0) {
    errors.push("Error: Payload length " + bytes.length + " does not match DEVICE_TYPE " + deviceType);
    return { data: readings, warnings: warnings, errors: errors };
  }

  var batteryPerc = decodeBattery(bytes[0], warnings);
  var batteryState = mapBatteryLevelPerc(batteryPerc);

  // OFFSET (last byte): number of intervals of length CyclicTransmissionCounter × SensorSampleTime
  // between the last dataset and the transmission time. 0 for regular transmissions.
  var offset = bytes[bytes.length - 1];
  var sampleMs = SENSOR_SAMPLE_TIME_MIN * 60 * 1000.0;
  var offsetMs = offset * CYCLIC_TRANSMISSION_COUNTER * sampleMs;

  for (var i = 0; i < datasetCount; i++) {
    var reading = { battery_perc_abs: batteryPerc, battery_state_abs: batteryState };
    var start = 1 + i * datasetLength;

    for (var key in BYTE_INDICES[deviceType]) {
      reading[key] = readValue(bytes, start + BYTE_INDICES[deviceType][key], key);
    }

    // first dataset is the oldest, the last one corresponds to the transmission time (minus offset)
    reading["offset_milliseconds"] = -((datasetCount - i - 1) * sampleMs + offsetMs);
    readings.push(reading);
  }

  return {
    data: readings,
    warnings: warnings,
    errors: errors
  };
}
```
