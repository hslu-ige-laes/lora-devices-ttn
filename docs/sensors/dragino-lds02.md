---
layout: default
title: Dragino :: LDS02
parent: Sensors
---

<img src="https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/dragino-lds02_01.jpg" width="250" align="right">

# Dragino - LDS02
{: .no_toc }

- Manufacturer: <a href="https://www.dragino.com/" target="_blank">Dragino</a>
- Product: <a href="https://www.dragino.com/products/lorawan-nb-iot-door-sensor-water-leak/item/181-lds02.html" target="_blank">LDS02</a>

The LDS02 is a LoRaWAN Window/Door sensor.

---

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Specifications
- indoor device
- Price ca. CHF 22.- (03.08.2023)
- Built-in sensors
  - <b>Magnetic reed switch</b>, [open/close]
- Power Supply: 2 common AAA batteries, 1.5 V
  - Expected life time: depending on usage, 16'000 ~ 70'000 state changes
- LoRaWAN version: 1.0.3
- LoRaWAN device class: A
- Size: 69.2 × 29.2 × 15 mm
- Weight: 55 g

---
## Documents
- [Payload description ttn v1.5 (2023-08-03)](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/dragino-lds02_04.txt)
- [Datasheet from dragino.com (2023-08-03)](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/dragino-lds02_02.pdf)
- <a href="http://wiki.dragino.com/xwiki/bin/view/Main/User%20Manual%20for%20LoRaWAN%20End%20Nodes/LDS02%20-%20LoRaWAN%20Door%20Sensor%20User%20Manual/" target="_blank">User Manual (online)</a>

---

## Ordering Info
- Part Number: LDS02-XX
  - XX is frequency band, for ttn in Europe choose EU868
- [Ordering Link](https://www.bastelgarage.ch/lds02-lorawan-door-fenster-sensor-node-868mhz)

---

## Adding the Device to TTN
- Before a device can communicate via "The Things Network" we have to add it to an application.<br>

1. [Create a new application](https://hslu-ige-laes.github.io/lora-devices-ttn/docs/getting_started#create-a-new-application)
2. Under `Overview` click `(+) Register device`
3. Under `Input method` select `Select the end device in the LoRaWAN Device Repository`
4. Enter the following device information
   - `End device brand` select `Dragino Technology Co., Limited`
   - `Model` select `LDS02`
   - `Hardware Ver.` select `Unknown Ver.` or whatever is possible or on the sticker
   - `Firmware` select `1.5` or whatever is possible or on the sticker
	 - `Profile (Region)` select `EU_863_870`
5. Under `Frequency plan` select `Europe 863-870 Mhz (SF9 for RX2 - recommended)`
6. Under `JoinEUI` enter the `App EUI` from the sticker
7. Enter as well the `DevEUI` and the `AppKey` from the sticker
8. Set an end-device name
9. Press `Register end device`
10. Open the case and take out the batteries
11. Replace the batteries and close the case

- Now the device should join the network and you can see the incoming telegrams in the `Live data` section
- The payload formatter should already be preset. If not, you can copy/paste it from below

---
## Payload formatter

```javascript
function decodeUplink(input) {
  var port = input.fPort;
  var bytes = input.bytes;
  var value=(bytes[0]<<8 | bytes[1])&0x3FFF;
  var bat=value/1000;//Battery,units:V
  
  var door_open_status=bytes[0]&0x80?1:0;//1:open,0:close
  var water_leak_status=bytes[0]&0x40?1:0;
  
  var mod=bytes[2];
  var alarm=bytes[9]&0x01;
  var data = {};
  	 switch (input.fPort) {
		 case 10:
  if(mod==1){
    var open_times=bytes[3]<<16 | bytes[4]<<8 | bytes[5];
    var open_duration=bytes[6]<<16 | bytes[7]<<8 | bytes[8];//units:min
    
      data.BAT_V=bat,
      data.MOD=mod,
      data.DOOR_OPEN_STATUS=door_open_status,
      data.DOOR_OPEN_TIMES=open_times,
      data.LAST_DOOR_OPEN_DURATION=open_duration,
     data.ALARM=alarm
    
  
}
  else if(mod==2)
  {
  var leak_times=bytes[3]<<16 | bytes[4]<<8 | bytes[5];
  var leak_duration=bytes[6]<<16 | bytes[7]<<8 | bytes[8];//units:min
  
      data.BAT_V=bat,
      data.MOD=mod,
      data.WATER_LEAK_STATUS=water_leak_status,
      data.WATER_LEAK_TIMES=leak_times,
      data.LAST_WATER_LEAK_DURATION=leak_duration
  
}
  else if(mod==3)
  
  {
      data.BAT_V=bat,
      data.MOD=mod,
      data.DOOR_OPEN_STATUS=door_open_status,
      data.WATER_LEAK_STATUS=water_leak_status,
      data.ALARM=alarm

  }

  else{
      data.BAT_V=bat,
      data.MOD=mod
  }
  return {
      data: data,
    }
	default:
    return {
      errors: ["unknown FPort"]
    }
}
}

```