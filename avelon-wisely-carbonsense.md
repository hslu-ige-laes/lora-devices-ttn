<img src="https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/wiki-files/avelon-wisely-carbonsense_01.png" width="250" align="right"><br>

- Manufacturer: <a href="https://avelon.com/" target="_blank">Avelon</a>
- Product: <a href="https://avelon.com/en/wisely/" target="_blank">Wisely Carbon Sense</a>

<quoteThe Wisely Carbonsense is an indoor room sensor to measure temperature, humidity, CO<sub>2</sub> and air pressure.

---

# Specifications
- Indoor device
- Price ca. CHF 263.- (14.07.2020)
- Sensors
  - <b>Temperature</b>, -10 ... +80 [°C], ± 0.1 °C between 20 ... 60 °C
  - <b>relative Humidity</b>, 0 ... 95[%rH], ± 1.5 %rH between 10...80 %rH
  - <b>CO<sub>2</sub></b>, 400 ... 5'000 [ppm], ± 30 ppm + 3 % of measurement value
  - <b>atmosheric pressure</b>, [hPa], ± 1 hPa
- Power Supply: 1 battery, 3.6 V, 3600 mAh, (A), Li-SOCl₂
  - Expected life time: 3 ... 5 years at roomtemperature
- Size: 80 × 80 × 27 mm
- Weight: 100 g

---

# Documents
  - [Payload description from avelon.cloud help (2020-07-14)](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/wiki-files/avelon-wisely-carbonsense_02.pdf)
  - [Datasheet from avelon.com (2020-07-14)](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/wiki-files/avelon-wisely-carbonsense_03.pdf)
  
---

# Ordering info
- The sensor comes in four different "flavours"
  - <b>~~Swisscom LPN~~</b> (means that the data goes to the AVELON server via Swisscom low power network, monthly charges apply)
  - <b>~~The Things Network~~</b> (means that the data goes to the AVELON-server via ttn, no monthly charges)
  - <b>~~Building Automation~~</b> (integration in the Avelon automation system, license fees system)
  - <b>Self-Managed</b> (that's what you wanna have!!! Data goes over the ttn network to the ttn server where you pick it up, no monthly charges or fees)
- [Ordering Link](https://avelon.com/en/buy-wisesly/)

---

# Device Configuration
## 1. Create an Account
> To register your device you’ll need a "The Things Network" account.<br>
> If you don't have one already, please follow these steps:

1. Go to [account.thethingsnetwork.org](https://account.thethingsnetwork.org/) and [click create an account](https://account.thethingsnetwork.org/register)
   - You will receive an email to confirm your email address. You have 24 hours to do so.
2. Select [Console](https://console.thethingsnetwork.org/) from the menue on top right
3. From the top right `menu`, select `your name > Settings`. Then change the `default Handler` if the one currently selected is not where you’ll be deploying most of your devices.

## 2. Add an Application
> To keep the payload decoder as simple as possible it is recommended to create an own application only for the avelon wisely carbonsense sensors. You can add multiple sensor of the same type to the application.

1. Log in and open the [Console > Application](https://console.thethingsnetwork.org/).
2. In the console, click [(+) add application](https://console.thethingsnetwork.org/applications/add)
   - For `Application ID`, choose a unique ID of lower case, alphanumeric characters and nonconsecutive - and _.
   - For `Application Description`, enter anything you like.

<img src="https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/wiki-files/avelon-wisely-carbonsense_04.png" width="700"><br>

3. Click `Add Application` to finish.

> You will be redirected to the newly added Application page.

## 3. Handler Change
> The Wisely sensors are per default configured for the Avelon Cloud, even if ordered as "self-managed". Thats why we have to detach the device from the avelon cloud.

1. Scan the `QR-Code` with e.g. a Mobile Phone
2. Click on the round `information (i) symbol` top right aside the sensor name
3. Press `Register for free`
4. Press `create new account`
5. select `Private`, fill in the fields and register
6. Assign the device and skip the `select location` as well as `alerting`
7. Change to your computer and log in to [https://avelon.cloud/login](https://avelon.cloud/login)
8. Click `your name > Devices` on top right
9. Your device should appear. Click on it to open the `Device Configuration`
10. Click on the `three small circles top right (More)` and choose `Configure LoraWAN Network Provider`
11. Choose `Self-Managed` and hit `OK`
12. Press the `blue floppy disk symbol` top right to save the configuration.
12. Copy the `Device EUI`, `Application EUI` and `Application Key`, we will need them later on.
13. Now we have to `reset the device manually` approximately 1-2 minutes after closing the previous dialog by pressing the small button on the back of the device. `5 seconds -> Off` , `2 seconds -> On`

## 4. Device Registration
> Before a device can communicate via "The Things Network" we need to register it with an application.<br>
> The Avelon Wisely sensors use the so called "Over The Air Activation" (OTAA) and for a secure communication we will need to register the beforehand copied keys.

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

## 5. Device Configuration
> Now you can see the incoming telegrams in the tab Data, but their content, the payload, is cryptic...!<br>
> We need to tell the "The Things Network" where to find e.g. the temperature in these cryptic numbers and letters. We can do that with configuring a "Payload Decoder Function".

1. [Log in](https://console.thethingsnetwork.org/applications) and open the `application`
2. Select the tab `Payload Formats > decoder` and copy/paste the following code:<br>

```javascript
function Decoder(bytes, port) {

  var decoded = {};

  // Offset
  OFFSET = bytes[bytes.length-1];
  
  // Data set length
  // Wisely Standard = 5; CarbonSense = 7; AllSense = 7
  DATASETLENGTH = 7;
  
  // number of data sets
  DATASETS = (bytes.length - 2 ) / 7;

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
  value = Math.round(value / DATASETS * 100) / 100;
  eval("decoded." + measurement + " = " + value + ";");
  
  // Temperature in °C
  measurement = "temp";
  value = 0;
  for (i = 0; i < DATASETS; i++) {
    value = value + (bytes[i * DATASETLENGTH + 3] << 8 | bytes[i * DATASETLENGTH + 4]) / 10.0;
  }
  value = Math.round(value / DATASETS * 100) / 100;
  eval("decoded." + measurement + " = " + value + ";");

  // Humidity in %rH
  measurement = "hum";
  value = 0;
  for (i = 0; i < DATASETS; i++) {
    value = value + bytes[i * DATASETLENGTH + 5] / 2.0;
  }
  value = Math.round(value / DATASETS * 100) / 100;
  eval("decoded." + measurement + " = " + value + ";");
    
  // Air quality CO2 in ppm
  measurement = "aqual";
  value = 0;
  for (i = 0; i < DATASETS; i++) {
    value = value + (bytes[i * DATASETLENGTH + 6] << 8 | bytes[i * DATASETLENGTH + 7]);
  }
  value = value / DATASETS;
  eval("decoded." + measurement + " = " + value + ";");

return decoded;
}
```
3. Copy/Paste the following test payload into the field `Payload` and press `Test`
```
FE25F3010B57029325F2010B56027325F3010A56027C25F3010A5702A825F2010A56028625F2010A56027800
```
4. You should see the following result
```json
{
  "aqual": 646.6666666666666,
  "batSta": "OK",
  "batVal": 100,
  "hum": 43.17,
  "press": 971.45,
  "temp": 26.63
}
```
> <b>aqual</b> -> Air Quality in [ppm]<br>
> <b>batSta</b> -> battery status ["OK";"OK, external power supply";"Error, could not acquire the voltage"]<br>
> <b>batVal</b> -> battery value [%]<br>
> <b>hum</b> -> Humidity [%rH]<br>
> <b>press</b> -> air pressure [hPa]<br>
> <b>temp</b> -> Temperature [°C]<br>

5. Press `save payload functions`

> Now you should be able to see the decoded data of your sensor in the tab `Data`.<br>
> Trigger a new telegram by pressing the reset-button on the Wisely for a short time (<2 seconds).<br><br>
> The Wisely sends a telegram once an hour with four 15 minutes measurements.<br>
> To keep the amount of data small the payload decoder takes these four measurements and saves the mean value with the timestamp of the last measurement.

## 6. Add Storage
> Normally incoming data in the network only gets forwarded to the end user applications and does not get saved.<br>
> "The Things Network" offers a seven day storage which is accessible through an API. To activate it, follow these steps.

1. [Log in](https://console.thethingsnetwork.org/applications) and open the `application`
2. Select the tab `Integrations > (+) add integration`
3. Select `Data Storage` and press `Add integration`

> Now we're done with the configuration :-)
