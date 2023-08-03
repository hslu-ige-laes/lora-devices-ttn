---
layout: default
title: Avelon - Wisely Standard
parent: Sensors
---

<img src="https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/avelon-wisely-standard_01.png" width="250" align="right" class="inline"/>

# Avelon - Wisely Standard
{: .no_toc }

- Manufacturer: <a href="https://avelon.com/" target="_blank">Avelon</a>
- Product: <a href="https://avelon.com/en/wisely/" target="_blank">Wisely Carbon Sense</a>

The Wisely Standard is an indoor room sensor to measure temperature, humidity and air pressure.

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
  - Expected life time: 3 ... 5 years at roomtemperature
- Size: 80 × 80 × 27 mm
- Weight: 80 g

---

## Documents
  - [Payload description from avelon.cloud help (2020-07-14)](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/avelon-wisely-standard_02.pdf)
  - [Datasheet from avelon.com (2020-07-14)](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/avelon-wisely-standard_03.pdf)
  
---

## Ordering Info
Attention, there are four different versions of the same sensor which have different software loaded on them. Only the "Self-Managed" version works with the lcm application. Do not order the version "The Things Network", it will not work with the lcm application.

- <b>Self-Managed -> that's what you want to have!!!</b><br>
  Data goes over the ttn network to the ttn server where the lcm application can fetch them, no monthly charges or fees.

Other variants which don't work with lcm
- <b>Swisscom LPN</b><br>
  Means that the data goes to the AVELON server via Swisscom low power network, monthly charges apply.
- <b>The Things Network</b><br>
  Means that the data goes via ttn, but to the AVELON-server! No monthly charges. In this case the lcm application can't access the data!
- <b>Building Automation</b><br>
  Integration in the Avelon automation system, license fees system.

[Ordering Link](https://avelon.com/en/wisely/)

---

## Adding the Device to TTN
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

### Device Registration
- Before a device can communicate via "The Things Network" we need to register it with an application.<br>
- The Avelon Wisely sensors use the so called "Over The Air Activation" (OTAA) and for a secure communication we will need to register the beforehand copied keys.

1. [Log in](https://console.thethingsnetwork.org/applications) and `open the application` to which you wish to add a device
2. Under `Settings > EUIs`  click `(+) add EUI`
3. Click the `pen symbol` and paste the before copied `Application EUI` from avelon.cloud and hit the button `Add EUI`
4. Click `Devices > register device`
   - For `Device ID`, choose a unique ID of lower case, alphanumeric characters and nonconsecutive - and _.
   - For `Device EUI` paste the copied "`Device EUI from avelon.cloud`
   - For `App Key` press the `pen icon` and paste the `Application Key from avelon.cloud`
   - Select the correct `App EUI` which we entered in step 3
   - press the button `Register`
5. Now we have to reset the device manually by pressing the small button on the back of the device. `5 seconds -> Off` , `2 seconds -> On`
6. The device should log in and you should see a green circle as `Status` in the tab `Device Overview`.
   - if not, please wait several hours and check again. The change of the handler can take a long time...

### Device Configuration
- Now you can see the incoming telegrams in the tab Data, but their content, the payload, is cryptic...!<br>
- We need to tell the "The Things Network" where to find e.g. the temperature in these cryptic numbers and letters. We can do that with configuring a "Payload Decoder Function".

1. [Log in](https://console.thethingsnetwork.org/applications) and open the `application`
2. Select the tab `Payload Formats > decoder` and copy/paste the following code:<br>
```javascript
function Decoder(bytes, port) {
  var decoded = {};
  if(port === 5){
    // Offset
    OFFSET = bytes[bytes.length-1];
    // Data set length
    // Wisely Standard = 5; CarbonSense = 7; AllSense = 7
    DATASETLENGTH = 5;
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

3. Copy/Paste the following test payload into the field `Payload`, enter **5** in the port-field on the right of the Payload and press `Test`
```
FE 25 37 00 E2 6D 25 37 00 E2 6D 00
```
<img src="https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/avelon-wisely-standard_05.png" width="700" class="inline"/><br>

4. You should see the following result
```json
{
  "batSta": "OK",
  "batVal": 100,
  "hum": 54.5,
  "press": 953,
  "temp": 22.6
}
```
  - <b>batSta</b>
    Battery status ["OK";"OK, external power supply";"Error, could not acquire the voltage"]<br>
  - <b>batVal</b>
    Battery value [%]<br>
  - <b>hum</b>
    Humidity [%rH]<br>
  - <b>press</b>
    Air pressure [hPa]<br>
  - <b>temp</b>
    Temperature [°C]<br>

5. Press `save payload functions`

- Now you should be able to see the decoded data of your sensor in the tab `Data`.<br>
- Trigger a new telegram by pressing the reset-button on the Wisely for a short time (<2 seconds).<br><br>
- The Wisely sends a telegram once an hour with four 15 minutes measurements.<br>
- To keep the amount of data small the payload decoder takes these four measurements and saves the mean value with the timestamp of the last measurement.

### Optional Settings
#### Change sending interval
- Per default the sensor measures each minute the values and sends a packet each hour.
- To change this, you have to send the device configuration telegrams.
- You have to send the so called downlink messages to port 10
- The device transmits its data after "CyclicTransmissionCounter" × "SensorSampleTime" starting from the last transmission.
- The example below sends data every 20 minutes with the settings
  - CyclicTransmissionCounter = 20
  - SensorSampleTime = 1

1. [Log in](https://console.thethingsnetwork.org/applications) and open the `application`
2. Select the tab `Devices` and select your device where you want to change the settings
3. Sroll down to "Downlink"
4. Select `FPort 10`
5. Add Payload for SensorSampleTime `FF 02 01` and press send
6. The wisely sensor only receives downlink data after a transmission. Therefore start a transmission by pressing the button on the back of the sensor (push once short, green led will illuminate)
7. Add Payload for CyclicTransmissionCounter `FF F0 14` and press send 
8. Press again the button on the back of the sensor

- Now the sampling interval should be changed.
- See the payload description for more details.


