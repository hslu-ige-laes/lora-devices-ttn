---
layout: default
title: Avelon - Wisely Standard
parent: Sensors
---

<img src="https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/avelon-wisely-standard_01.png" width="250" align="right" class="inline"/>

# Avelon - Wisely Standard
{: .no_toc }

- Manufacturer: <a href="https://avelon.com/" target="_blank">Avelon</a>
- Product: <a href="https://avelon.com/en/wisely/" target="_blank">Wisely Standard</a>

The Wisely Standard is an indoor room sensor to measure temperature, humidity and atmospheric pressure.

---
## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Specifications
- Indoor device
- Price ca. CHF 68.- (25.05.2022)
- Sensors
  - <b>Temperature</b>, -10 ... +80 [°C], ± 0.1 °C between 20 ... 60 °C
  - <b>relative Humidity</b>, 0 ... 95[%rH], ± 1.5 %rH between 10...80 %rH
  - <b>atmosheric pressure</b>, [hPa], ± 1 hPa
- Power Supply: 1 battery, 3.6 V, 2600 mAh, (AA), Li-SOCl₂
  - Expected life time: 3 ... 8 years at roomtemperature
- Size: 80 × 80 × 27 mm
- Weight: 80 g

---

## Documents / Links
- [Payload description from avelon (online)](https://avelon.cloud/docs/de/appendix/wisely-payload.html)
- [Wisely Datasheet from avelon.com (2020-07-14)](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/avelon-wisely-carbonsense_03.pdf)
- [FAQ on avelon.com](https://avelon.com/support/wisely-faq/)
- [Temp- and Humidity Sensor Datasheet - Sensirion SHT3x](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/avelon-wisely-sensirion-sht3x.pdf)
- [Atmospheric Pressure Sensor Datasheet - Bosch BMP380](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/avelon-wisely-bosch-bmp280.pdf)

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

- <b>Self-Managed -> that's what you want to have!!!</b><br>
  Data goes over the ttn network to the ttn server where the lcm application can fetch them, no monthly charges or fees.

**Other variants which don't work with TTN**
- <b>Swisscom LPN</b><br>
  Means that the data goes to the AVELON server via Swisscom low power network, monthly charges apply.
- <b>The Things Network</b><br>
  Means that the data goes via ttn, but to the AVELON-server! No monthly charges. In this case the lcm application can't access the data!
- <b>Building Automation</b><br>
  Integration in the Avelon automation system, license fees system.

[Ordering Link](https://avelon.com/en/wisely/)

---

## Handler Change
- The Wisely sensors are per default configured for the Avelon Cloud, even if ordered as "self-managed". Thats why we have to detach the device from the avelon cloud.

1. Switch the device on and scan the `QR-Code` with e.g. a Mobile Phone 
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
2. Under `Overview` click `(+) Register device`
3. Under `Input method` select `Enter end device specifics manually`
4. Under `Frequency plan` select `Europe 863-870 Mhz (SF9 for RX2 - recommended)`
5. Under `LoRaWAN version` select `LoRaWAN Specification 1.0.3`
6. As `JoinEUI` enter the `Application EUI`, fill in as well the `Device EUI` and the `Application Key`
7. Set an end-device name
8. Press `Register end device`
9. Add the payload formatter
   - Switch to the tab `Payload formatters`
   - As `Formatter type` select `Custom Javascript formatter`   
   - Copy/Paste the code from the payload Formatter section below
   - Click `Save changes`
10. Now we have to reset the device manually by pressing the small button on the back of the device. `5 seconds -> Off` , `2 seconds -> On`
11. The device should log in and you should see a green circle as `Status` in the tab `Device Overview`.
    - if not, please wait several hours and check again. The change of the handler can take a long time...

---

## Optional Settings

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
- SensorSampleTime = 1 (HEX 01 -> `FF 01 01`)
  Every 1 min a sensor sample, every 10 min an averaged measurement.
- CyclicTransmissionCounter = 60 (HEX 3C -> `FF F0 3C`)
  After 60 samples a data transmission, that means 6 averaged measurements in the payload, transmitted every 60 minutes.

**Example 2**<br>
This configuration makes sense if the smallest possible measuring rate is required, but battery life should get optimized a little bit. 

- SensorSampleTime = 10 (HEX 0A -> `FF 01 0A`)
  Every 10 min a sensor sample, every 10 min an averaged measurement.
- CyclicTransmissionCounter = 6 (HEX 06 -> `FF F0 06`)
  After 6 samples a data transmission, that means 6 averaged measurements in the payload, transmitted every 60 minutes.

**Example 3**<br>
This configuration makes sense if a long battery life is required .
The sensor acts as a logger and the data is not real time, but battery life is maximized.
Small changes in sensor values cannot get detected, so the accuracy is slightly lower.
E.g. the impact of an opened window might not be visible in the temperature data.

- SensorSampleTime = 60 (HEX 3C -> `FF 01 3C`)
  Every 60 min a sensor sample, every 60 min an averaged measurement.
- CyclicTransmissionCounter = 24 (HEX 18 -> `FF F0 18`)
  After 6 samples a data transmission, that means 6 averaged measurements in the payload, transmitted every 360 minutes (6 hours).

---

## Payload formatter

```javascript
function getValues(bytes, measurement, byteIndices, deviceType, datasetCount, datasetLength, payloadOffset) {
	var decoded = [];
	var measurementByteLengths = {"pressure_hPa": 2, "temp_degrC": 2, "hum_relH": 1, "voc_index": 2, "brgt_lux": 2, "co2": 2, "presence_min": 2};
  var divFactors = {"pressure_hPa": 10.0, "temp_degrC": 10.0, "hum_relH": 2.0, "voc_index": 1.0, "brgt_lux": 1.0, "co2": 1.0, "presence_min": 1.0};

	if (measurement in byteIndices[deviceType]){
		byteIndexValue = byteIndices[deviceType][measurement];

		for (i = 0; i < datasetCount; i++) {
			byteIndex = i * datasetLength + byteIndexValue + payloadOffset;
			if (measurementByteLengths[measurement] === 2) {
				decoded.push((bytes[byteIndex] << 8 | bytes[byteIndex + 1]) / divFactors[measurement]);
			}
			else {
				decoded.push((bytes[byteIndex]) / divFactors[measurement]);
			}
		}
	}
	return decoded;
}

function decodeUplink(input) {
  var deviceType = "Standard";
  var datasetLengthDict = {
    "Standard": 5,
    "CarbonSense": 7,
    "AllSense": 7,
    "AllSenseExt": 13
  };
  var byteIndices = {
    "Standard": {"pressure_hPa": 0, "temp_degrC": 2, "hum_relH": 4},
    "CarbonSense": {"pressure_hPa": 0, "temp_degrC": 2, "hum_relH": 4, "co2": 5},
    "AllSense": {"temp_degrC": 0, "hum_relH": 2, "voc_index": 3, "co2": 5},
    "AllSenseExt": {"pressure_hPa": 0, "temp_degrC": 2, "hum_relH": 4, "voc_index": 5, "brgt_lux": 7, "co2": 9, "presence_min": 11}
  };
  var payloadOffset = 1; // Offset of battery information, offset before datasets
  var data = {};
  var payload = [];
  var byteIndex = 0;
  var byteIndexValue = 0;
  var i = 0;
  var measurement = "";
  
  var warnings = [];
  var errors = [];
  
  if(datasetLengthDict[deviceType] === undefined){
    errors.push("Error: Typo in deviceType configuration in payload decoder");
  }
  
  if((input.fPort === 5) || (input.fPort === 6)){
    data.deviceName = "Wisely " + deviceType;
    data.dataOffset = input.bytes[input.bytes.length-1];
    
    // Battery status and percent
    var batVal = input.bytes[0];
    if((batVal >= 30) && (batVal < 254)){
      data.battery_perc = Math.round(batVal / 254.0 * 100.0 *10) / 10;
      data.battery_status = "OK";
    }else if((batVal < 30) && (batVal > 1)){
      data.battery_perc = Math.round(batVal / 254.0 * 100.0 *10) / 10;
      data.battery_status = "Low battery state";
      warnings.push("Battery Warning: Low state");
    }else if((batVal === 1)){
      data.battery_perc = 1;
      data.battery_status = "No further battery capacity available";
      warnings.push("Battery Warning: No further battery capacity available");
    }else if(batVal === 254){
      data.battery_perc = 100.0;
      data.battery_status = "Battery at maximum capacity";
    } else{
      data.battery_perc = 0.0;
      data.battery_status = "Could not acquire the battery voltage";
      warnings.push("Battery Warning: Could not acquire the voltage");
    }
      
    if(input.fPort === 5){
      data.payloadFormat = "Standard uplink payload";
    }else{ // fPort === 6
      data.payloadFormat = "Extended uplink payload";
      if(deviceType !== "AllSense"){
        warnings.push("Warning: deviceType in payload decoder is no set to allsense");
      }
      deviceType = "AllSenseExt";
      data.deviceName = "Wisely AllSense (Extended Payload)";
    }
  }else{
    errors.push("Unknown fPort");
  }
  
  if((input.fPort === 5) || (input.fPort === 6)){
    data.datasetLength = datasetLengthDict[deviceType];
    data.datasetCount = (input.bytes.length - 2) / data.datasetLength;
    
    if(!Number.isInteger(data.datasetCount)) {
      errors.push("Error: datasetCount is not a whole number!");
    }
    
    for (const [key, value] of Object.entries(byteIndices[deviceType])) {
    	decoded = getValues(input.bytes, key, byteIndices, deviceType, data.datasetCount, data.datasetLength, payloadOffset);
    	if (decoded.length > 0) {
    		data[key] = decoded;
    	}
    }
  }

  return {
    data: data,
    warnings: warnings,
    errors: errors
  };
}
```


