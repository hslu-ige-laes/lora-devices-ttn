---
layout: default
title: Dragino - LDS02
parent: Sensors
---

<img src="https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/dragino-lds02_01.jpg" width="250" align="right">

# Dragino - LDS02
{: .no_toc }

- Manufacturer: <a href="https://www.dragino.com/" target="_blank">Dragino</a>
- Product: <a href="https://www.dragino.com/products/lorawan-nb-iot-door-sensor-water-leak/item/181-lds02.html" target="_blank">LDS02</a>

The LDS02 is a LoRaWAN Window/Door Sensor.

---

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

# Specifications
- indoor device
- Price ca. CHF 30.- (03.08.2023)
- Built-in sensors
  - <b>Magnetic reed switch</b>, [open/close]
- Power Supply: 2 common AAA batteries, 1.5 V
  - Expected life time: depending on usage, 16'000 ~ 70'000 state changes
- LoRaWAN version: 1.0.3
- LoRaWAN device class: A
- Size: 69.2 × 29.2 × 15 mm
- Weight: 55 g

---
# Documents
  - [Payload description ttn v1.5 (2023-08-03)](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/dragino-lds02_04.txt)
  - [Datasheet from dragino.com (2023-08-03)](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/dragino-lds02_02.pdf)
	- <a href="http://wiki.dragino.com/xwiki/bin/view/Main/User%20Manual%20for%20LoRaWAN%20End%20Nodes/LDS02%20-%20LoRaWAN%20Door%20Sensor%20User%20Manual/" target="_blank">User Manual (online)</a>

---

# Ordering info
- Part Number: LDS02-XX
  - XX is frequency band, for ttn in Europe choose EU868
- [Ordering Link](https://www.bastelgarage.ch/lds02-lorawan-door-fenster-sensor-node-868mhz)

---
# Device Configuration
## Device Registration
> Before a device can communicate via "The Things Network" we have to add it to an application.<br>

1. [Create a new application](https://hslu-ige-laes.github.io/lora-devices-ttn/docs/getting_started#create-a-new-application)
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

## Device Configuration
> Now you can see the incoming telegrams in the tab Data, but their content, the payload, is cryptic...!<br>
> We need to tell the "The Things Network" where to find e.g. the temperature etc. in these cryptic numbers and letters. We can do that with configuring a "Payload Decoder Function".

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
> <b>BatV</b> -> battery voltage [V]<br>
> <b>Ext_sensor</b> -> connected external sensor<br>
> <b>Hum_SHT</b> -> Humidity internal sensor [%rH]<br>
> <b>TempC_DS</b> -> Temperature external sensor [°C]<br>
> <b>TempC_SHT</b> -> Temperature internal sensor [°C]<br>

5. Press `save payload functions`

> Now you should be able to see the decoded data of your sensor in the tab `Data`.<br>
> Trigger a new telegram by pressing the ACT-button on the device for a short time (> 1s and < 3s).<br><br>
> The device sends a telegram once every 10 minutes.<br>


