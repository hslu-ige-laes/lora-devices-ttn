---
layout: default
title: Dragino - LHT65
parent: Sensors
---

<img src="https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/dragino-lht65_01.png" width="250" align="right">

# Dragino - LHT65
{: .no_toc }

- Manufacturer: <a href="https://www.dragino.com/" target="_blank">Dragino</a>
- Product: <a href="https://www.dragino.com/products/lora-lorawan-end-node/item/151-lht65.html" target="_blank">LHT65</a>

The LHT65 includes a built-in SHT20 temperature/humidity sensor and a jack to connect an external sensor e.g. a temperature sensor.

---

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Specifications
- indoor device (external sensor placement possible)
- Price ca. CHF 50.- (15.07.2020)
- Built-in sensors
  - <b>Temperature</b>, -40 ... +80 [°C], ± 0.8
  - <b>relative Humidity</b>, 0 ... 99.9 [%rH], ± 10 %rH
- External sensor
  - <b>Temperature</b>, -55 ... 125 [°C], ± 0.5 °C between -10 ... 85 °C, ± 2 °C between -55 ... 125 °C
- Power Supply: 1 battery, 3.0 V, 2400 mAh (CR17450), Li-MnO₂
  - Expected life time: 8 ... 10 years at roomtemperature
- Min voltage: 2.45 V
- Size: 43 × 93 × 28 mm
- Weight: 116 g

---
## Documents
  - [Payload description v1.7 (2020-07-15)](https://www.dragino.com/downloads/downloads/LHT65/payload_decode/ttn_payload_decode_v1.7.txt)
  - [Datasheet from dragino.com (2020-07-15)](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/dragino-lht65_02.pdf)
  - [User manual from dragino.com (2020-07-15)](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/dragino-lht65_03.pdf)

---

## Ordering Info
- Part Number: LHT65-XX-YY
  - XX is frequency band
  - YY is external sensor
- Version with external temperature sensor: <b>LHT65-EU868-E1</b>
- [Ordering Link](https://www.bastelgarage.ch/lht65-lorawan-temperatur-und-humidity-sensor)

---

## Adding the Device to TTN
### Handler device information
Each device is shipped with a sticker with the default keys on it:

<img src="https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/dragino-lht65_04.png" width="400"><br>

- For an OTAA (over the air activation) only the `DEV EUI`, `APP EUI` and `APP KEY` are required.

### Device Registration
- Before a device can communicate via "The Things Network" we need to register it with an application.<br>
- The dragino sensors use the so called "Over The Air Activation" (OTAA) and for a secure communication we will need to register the beforehand mentioned keys.

1. [Log in](https://console.thethingsnetwork.org/applications) and `open the application` to which you wish to add a device
2. Under `Settings > EUIs`  click `(+) add EUI`
3. Click the `pen symbol` and paste the before copied `APP EUI` from the sticker and hit the button `Add EUI`
4. Click `Devices > register device`
   - For `Device ID`, choose a unique ID of lower case, alphanumeric characters and nonconsecutive - and _.
   - For `Device EUI` paste the copied "`DEV EUI from the sticker`
   - For `App Key` press the `pen icon` and enter the `APP KEY from the sticker`
   - Select the correct `App EUI` which we entered in step 3
   - press the button `Register`
5. Now we have to restart the device manually by pressing the ACT button on the bottom of the device. `fast press ACT 5 times -> Off` , `press ACT for more than 3 seconds -> On`
6. The device should log in and you should see a green circle as `Status` in the tab `Device Overview`.

### Device Configuration
- Now you can see the incoming telegrams in the tab Data, but their content, the payload, is cryptic...!<br>
- We need to tell the "The Things Network" where to find e.g. the temperature etc. in these cryptic numbers and letters. We can do that with configuring a "Payload Decoder Function".

1. [Log in](https://console.thethingsnetwork.org/applications) and open the `application`
2. Select the tab `Payload Formats > decoder` and copy/paste the following code:

```javascript
function Decoder(bytes, port) {
//Payload Formats of LHT65 Device
  return {
    
       //External sensor
       Ext_sensor:
       {
         "0":"No external sensor",
         "1":"Temperature Sensor",
         "4":"Interrupt Sensor send",
         "5":"Illumination Sensor",
         "6":"ADC Sensor",
         "7":"Interrupt Sensor count",
       }[bytes[6]&0x7F],
       
       //Battery,units:V
       BatV:((bytes[0]<<8 | bytes[1]) & 0x3FFF)/1000,
       
       //SHT20,temperature,units:â„ƒ
       TempC_SHT:((bytes[2]<<24>>16 | bytes[3])/100).toFixed(2),
       
       //SHT20,Humidity,units:%
       Hum_SHT:((bytes[4]<<8 | bytes[5])/10).toFixed(1),
       
       //DS18B20,temperature,units:â„ƒ
       TempC_DS:
       {
         "1":((bytes[7]<<24>>16 | bytes[8])/100).toFixed(2),
       }[bytes[6]&0xFF],       
       
       //Exti pin level,PA4
       Exti_pin_level:
       {
         "4":bytes[7] ? "High":"Low",
       }[bytes[6]&0x7F], 
       
       //Exit pin status,PA4
       Exti_status:
       {
         "4":bytes[8] ? "True":"False",
       }[bytes[6]&0x7F],    
       
       //BH1750,illumination,units:lux
       ILL_lux:
       {
         "5":bytes[7]<<8 | bytes[8],
       }[bytes[6]&0x7F],  

        //ADC,PA4,units:V
        ADC_V:
       {
         "6":(bytes[7]<<8 | bytes[8])/1000,
       }[bytes[6]&0x7F],  
       
        //Exti count,PA4,units:times
        Exit_count:
        {
          "7":bytes[7]<<8 | bytes[8],
        }[bytes[6]&0x7F],  
        
        //Applicable to working mode 4567,and working mode 467 requires short circuit PA9 and PA10
        No_connect:
        {
          "1":"Sensor no connection",
        }[(bytes[6]&0x80)>>7],  
  };
}
```

3. Copy/Paste the following test payload into the field `Payload` and press `Test`
```
CC B7 0A 6C 03 9F 01 0A 85 7F FF
```
4. You should see the following result
```json
{
  "BatV": 3.255,
  "Ext_sensor": "Temperature Sensor",
  "Hum_SHT": "92.7",
  "TempC_DS": "26.93",
  "TempC_SHT": "26.68"
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