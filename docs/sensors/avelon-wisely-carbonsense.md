---
layout: default
title: Avelon - Wisely Carbons.
parent: Sensors
---

<img src="https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/avelon-wisely-carbonsense_01.png" width="250" align="right" class="inline"/>

# Avelon - Wisely Carbonsense
{: .no_toc }

- Manufacturer: <a href="https://avelon.com/" target="_blank">Avelon</a>
- Product: <a href="https://avelon.com/en/wisely/" target="_blank">Wisely Carbon Sense</a>

The Wisely Carbonsense is an indoor room sensor to measure temperature, humidity and CO2.

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
  - **atmosheric pressure**, [hPa], ± 1 hPa
- Power Supply: 1 battery, 3.6 V, 3600 mAh, (A), Li-SOCl₂
  - Expected life time: 3 ... 5 years at roomtemperature
- Size: 80 × 80 × 27 mm
- Weight: 100 g

---

## Documents
- [Payload description from avelon.cloud help (2020-07-14)](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/avelon-wisely-carbonsense_02.pdf)
- [Datasheet from avelon.com (2020-07-14)](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/avelon-wisely-carbonsense_03.pdf)
  
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
- If an adjustable CO2 limit is exceeded, the LED on the front side of the device blinks every 60 seconds (250 ms lighting, 500 ms pause - repeating 4 times).
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
function Decoder(bytes, port) {
  var decoded = {};
  if(port === 5){
    // Offset
    OFFSET = bytes[bytes.length-1];
    // Data set length
    // Wisely Standard = 5; CarbonSense = 7; AllSense = 7
    DATASETLENGTH = 7;
    // number of data sets
    DATASETS = (bytes.length - 2 ) / DATASETLENGTH;
    // Battery status
    var BAT = bytes[0]
    if((BAT > 0) && (BAT < 255)){
      var batVal = BAT / 254.0 * 100.0;
      decoded.batVal = Math.round(batVal * 10) / 10;
      decoded.batSta = "OK";
    }else if(BAT === 0){ // no battery inserted, external power supply
      decoded.batVal = 100.0;
      decoded.batSta = "OK, external power supply";
    } else{ // device could not acquire the voltage
      decoded.batVal = 0.0;
      decoded.batSta = "Error, could not acquire the voltage";
    }
    var measurement = "";
    var value = "";
    // atmospheric pressure in hPa
    measurement = "press";
    value = 0;
    for (i = 0; i < DATASETS; i++) {
      value = value + (bytes[i * DATASETLENGTH + 1] << 8 | bytes[i * DATASETLENGTH + 2]) / 10.0;
    }
    value = Math.round(value / DATASETS * 1) / 1;
    eval("decoded." + measurement + " = " + value + ";");
    // Temperature in °C
    measurement = "temp";
    value = 0;
    for (i = 0; i < DATASETS; i++) {
      value = value + (bytes[i * DATASETLENGTH + 3] << 8 | bytes[i * DATASETLENGTH + 4]) / 10.0;
    }
    value = Math.round(value / DATASETS * 10) / 10;
    eval("decoded." + measurement + " = " + value + ";");
    // Humidity in %rH
    measurement = "hum";
    value = 0;
    for (i = 0; i < DATASETS; i++) {
      value = value + bytes[i * DATASETLENGTH + 5] / 2.0;
    }
    value = Math.round(value / DATASETS * 10) / 10;
    eval("decoded." + measurement + " = " + value + ";");
    if(DATASETLENGTH != 5){
      // Air quality CO2 in ppm
      measurement = "co2";
      value = 0;
      for (i = 0; i < DATASETS; i++) {
  	  value = value + (bytes[i * DATASETLENGTH + 6] << 8 | bytes[i * DATASETLENGTH + 7]);
      }
      value = Math.round(value / DATASETS * 1) / 1;
      eval("decoded." + measurement + " = " + value + ";");
    }
  }
return decoded;
}
```
