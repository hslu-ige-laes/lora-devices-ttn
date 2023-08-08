---
layout: default
title: Avelon :: Wisely CarbonSense
parent: Sensors
---

<img src="https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/avelon-wisely-carbonsense_01.png" width="250" align="right" class="inline"/>

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
- Power Supply: 1 battery, 3.6 V, 3600 mAh, (A), Li-SOCl₂
  - Expected life time: 3 ... 5 years at roomtemperature
- Size: 80 × 80 × 27 mm
- Weight: 100 g

---

## Documents
- [Payload description from avelon.cloud help (2020-07-14)](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/avelon-wisely-carbonsense_02.pdf)
- [Datasheet from avelon.com (2020-07-14)](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/avelon-wisely-carbonsense_03.pdf)

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

* Due to limitations in payload length, these channels are not activated by default
  
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
### CO<sub>2</sub> Sensor ABC Algorithm
The ABC algorithm in CO<sub>2</sub> sensors uses automatic base correction to reduce fluctuations and drifts in CO<sub>2</sub> measurements.
It continuously determines the zero point of the CO<sub>2</sub> signal and adjusts it to 400 ppm (more or less the outside level) to ensure accurate and stable detection of CO<sub>2</sub> concentrations in ambient air.
This improves the long-term stability and reliability of CO<sub>2</sub> sensors.

However, this means that the sensor must be exposed to clean air from time to time. Otherwise the CO<sub>2</sub> value will drift.

The sensor determines this base value during the first 2 weeks of operation and only provides usable values after this time. It is therefore important that the sensor has been exposed to the outside air for more than 2 hours during this time. If you want to accelerate this process, you can expose the sensor to the outside air and then send a downlink payload 0xB2. This is how you end the 2 weeks period.

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

### Change sending interval
- Per default the sensor measures each minute the values and sends a packet each hour.
- To change this, you have to send the device configuration telegrams.
- You have to send the so called downlink messages to port 10
- The device transmits its data after "CyclicTransmissionCounter" × "SensorSampleTime" starting from the last transmission.
- The example below sends data every 20 minutes with the settings
  - CyclicTransmissionCounter = 20
  - SensorSampleTime = 1

1. Select the device and change to the tab `Messaging`, select `Downlink`
2. Change the `FPort to 10`
3. Copy/paste `FF 02 01` into the `Payload` field for changing SensorSampleTime
4. Press `Send`
5. In the `Data` tab you should now see the scheduled telegram. The wisely sensor only receives downlink data after a transmission. Therefore start a transmission by pressing the button on the back of the sensor (push once short, green led will illuminate)
6. Copy/paste `FF F0 14` into the `Payload` field for changing CyclicTransmissionCounter
7. Press `Send`
8. Press again the button on the back of the sensor

- Now the sampling interval should be changed.
- See the payload description for more details.


### Change the led blinking behaviour
- If an adjustable CO<sub>2</sub> limit is exceeded, the LED on the front side of the device blinks every 60 seconds (250 ms lighting, 500 ms pause - repeating 4 times).
- With the following procedure you can deactivate it.

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

---

## Payload formatter

```javascript
function getValues(bytes, measurement, byteIndices, deviceType, datasetCount, datasetLength, payloadOffset) {
	var payload = [];
	var measurementByteLengths = {"pressure": 2, "temperature": 2, "humidity": 1, "voc": 2, "brightness": 2, "co2": 2, "presence": 2};
  var divFactors = {"pressure": 10.0, "temperature": 10.0, "humidity": 2.0, "voc": 1.0, "brightness": 1.0, "co2": 1.0, "presence": 1.0};

	if (measurement in byteIndices[deviceType]){
		byteIndexValue = byteIndices[deviceType][measurement];

		for (i = 0; i < datasetCount; i++) {
			byteIndex = i * datasetLength + byteIndexValue + payloadOffset;
			if (measurementByteLengths[measurement] === 2) {
				payload.push((bytes[byteIndex] << 8 | bytes[byteIndex + 1]) / divFactors[measurement]);
			}
			else {
				payload.push((bytes[byteIndex]) / divFactors[measurement]);
			}
		}
	}
	return payload;
}

function decodeUplink(input) {
  var deviceType = "CarbonSense";
  var datasetLengthDict = {
    "Standard": 5,
    "CarbonSense": 7,
    "AllSense": 7,
    "AllSenseExt": 13
  };
  var byteIndices = {
    "Standard": {"pressure": 0, "temperature": 2, "humidity": 4},
    "CarbonSense": {"pressure": 0, "temperature": 2, "humidity": 4, "co2": 5},
    "AllSense": {"temperature": 0, "humidity": 2, "voc": 3, "co2": 5},
    "AllSenseExt": {"pressure": 0, "temperature": 2, "humidity": 4, "voc": 5, "brightness": 7, "co2": 9, "presence": 11}
  };
  var payloadOffset = 1; // Offset of battery information, offset before datasets
  var data = {};
  data.payload = {};
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
      data.batteryPerc = Math.round(batVal / 254.0 * 100.0 *10) / 10;
      data.batteryStatus = "OK";
    }else if((batVal < 30) && (batVal > 1)){
      data.batteryPerc = Math.round(batVal / 254.0 * 100.0 *10) / 10;
      data.batteryStatus = "Low battery state";
      warnings.push("Battery Warning: Low state");
    }else if((batVal === 1)){
      data.batteryPerc = 1;
      data.batteryStatus = "No further battery capacity available";
      warnings.push("Battery Warning: No further battery capacity available");
    }else if(batVal === 254){
      data.batteryPerc = 100.0;
      data.batteryStatus = "Battery at maximum capacity";
    } else{
      data.batteryPerc = 0.0;
      data.batteryStatus = "Could not acquire the battery voltage";
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
      errors.push("Error: datasetLength is not a whole number!");
    }
    
    for (const [key, value] of Object.entries(byteIndices[deviceType])) {
    	payload = getValues(input.bytes, key, byteIndices, deviceType, data.datasetCount, data.datasetLength, payloadOffset);
    	if (payload.length > 0) {
    		data.payload[key] = payload;
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
