---
layout: default
title: Dragino - LTC2-LB
parent: Sensors
---

<img src="https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/dragino-ltc2-lb_01.PNG" width="250" align="right">

# Dragino - LTC2-LB
{: .no_toc }

- Manufacturer: <a href="https://www.dragino.com/" target="_blank">Dragino</a>
- Product: <a href="https://www.dragino.com/products/temperature-humidity-sensor/item/343-ltc2-lb.html" target="_blank">LTC2-LB</a>

The LTC2-LB is a LoRaWAN Waterproof Outdoor Temperature Sensor with 2 external PT100 Probes.

---

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Specifications

- indoor/outdoor device
- Attention, LTC2-LB doesn't include temperature sensors, you need to purchase them separately
- Price LTC2-LB ca. CHF 66.- (07.04.2025) without sensors
- 2 External **Temperature** Sensors PT100 (support 3-wire), Cable Length 2m
  - DR-SI – Standard IP68
	  - Price each ca. CHF 15.- (07.04.2025)
		- -60 ... +200 [°C], Class A, IP68
  - DR-LT – Low Temperature 
	  - Price each ca. CHF 16.- (07.04.2025)
		- -196 ... +150 [°C], Class A, IP68
  - DR-HT – High Temperature
	  - Price each ca. CHF 66.- (07.04.2025)
		- -70 ... +450 [°C], Class A, IP68
  - DR-FSA – Food Safety Type
	  - Price each ca. CHF 66.- (07.04.2025)
		- -40 ... +260 [°C], Class A, IP68
  - DR-FT – **Flat Type** - ideal to mount on pipes
	  - Price each ca. CHF 15.- (07.04.2025)
		- -60 ... +200 [°C], Class A, IP68
- Power Supply: 1 built in 8500mAh Li-SOCI2 battery
  - Expected life time: depending on usage, 5 ... 10 years
- LoRaWAN version: 1.0.3
- LoRaWAN device class: A
- Protection: [IP66](https://en.wikipedia.org/wiki/IP_Code)
- Operating Temperature: -40 ... 85°C
- Size: 145 × 102 × 51 mm
- Weight: 271 g

---

## Documents/Links
- [Payload description (2023-08-09)](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/dragino-ltc2-lb_03.txt)
- [Datasheet from dragino.com (2023-08-09)](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/dragino-ltc2-lb_02.pdf)
- [User Manual (online)](https://wiki.dragino.com/xwiki/bin/view/Main/User%20Manual%20for%20LoRaWAN%20End%20Nodes/LTC2-LB--LoRaWAN_Temperature_Transmitter_User_Manual/)

---

## Ordering Info
- [LTC2-LB-EU868](https://www.bastelgarage.ch/ltc2-lb-lorawan-temperatur-transmitter-node-868mhz)
- [DR-SI – Standard IP68](https://www.bastelgarage.ch/dr-si-pt100-temperatur-sensor-standard)
- [DR-LT – Low Temperature](https://www.bastelgarage.ch/dr-lt-pt100-low-temperatur-sensor)
- [DR-HT – High Temperature](https://www.bastelgarage.ch/dr-ht-pt100-high-temperatur-sensor)
- [DR-FSA – Food Safety Type](https://www.bastelgarage.ch/dr-fsa-pt100-temperatur-sensor-food)
- [DR-FT – Flat Type](https://www.bastelgarage.ch/dr-ft-pt100-temperatur-sensor-flach)

---

## Button Actions, Modes and LED States

![Device Overview](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/dragino-ltc2-lb_04.PNG "Device Overview")

The LTC2-LB has two operating modes:
- In **Deep Sleep Mode**, which is the default mode used for storage and shipping, the device does not perform any LoRaWAN activity to conserve battery life.
- In **Working Mode**, the device joins a LoRaWAN network and sends sensor data to the server.
- Between each sampling, transmission, and reception cycle, it enters **STOP mode** (also known as IDLE mode), which consumes the same low power as Deep Sleep Mode.

The ACT push button on the device is used to switch between the above mentioned modes.

| Action                          | Description                                                                                   | LED Status                                                                 |
|---------------------------------|-----------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------|
| Press and hold **1–3s**         | Sends uplink if joined. BLE is active for configuration.                                      | **Blue LED blinks once**                                                    |
| Press and hold **> 3s**         | Activates device. Starts join procedure. BLE is active regardless of join result.             | **Green LED blinks fast 5 times**, then **solid green for 5 seconds**      |
| Fast press **5 times**          | Deactivates device. Enters Deep Sleep Mode.                                                   | **Red LED solid on for 5 seconds**                                          |

---

## Adding the Device to TTN
- The `JoinEUI`, `App EUI` and the `DevEUI` should be on a sticker on the cardboard box.
- Before a device can communicate via "The Things Network" we have to add it to an application.<br>

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
10. Switch on the device, see above table

- After Configuration, the device restarts automatically and tries to join the network
- Now the device should join the network and you can see the incoming telegrams in the `Live data` section

---

## Optional Settings

### Change sampling interval
To change the sampling interval, you have to send the device configuration telegrams (Downlink-Messages)
The time interval in minutes at which the sensor queries the current values.

1. In the TTN Console on the device view, select the device and change to the tab `Messaging`, select `Downlink`
2. Change the `FPort to 2`
3. Copy/paste the payload, e.g. `01000258` into the `Payload` field to set interval to 10 minutes
4. Press `Send`
5. In the `Data` tab you should now see the scheduled telegram. The device only receives downlink data after a transmission. Therefore start a transmission by pressing the button on the back of the sensor (push once short, green led will illuminate)

#### Examples
'0100' is an identifier, the rest represents the sampling interval in hex

-	5 Minutes Interval:  '0100**012C**' (300s in hex are '012C')
-	10 Minutes Interval:  '0100**0258**' (600s in hex are '0258')
-	15 Minutes Interval: '0100**0384**' (900s in hex are '0384')
-	60 Minutes Interval: '0100**0E10**' (3600s in hex are '0E10')

---

## Payload Decoder

```javascript
function mapBatteryVoltageAbs(voltage) {
  if (voltage < 3.35) {
    return 0; // Critical
  } else if (voltage < 3.45) {
    return 1; // Warning
  } else if (voltage < 3.55) {
    return 2; // Good
  } else {
    return 3; // Very Good
  }
}

function decodeUplink(input) {
  const port = input.fPort;
  const bytes = input.bytes;
  const data = {};

  function toTemperature(byteH, byteL) {
    const value = parseFloat(((byteH << 24 >> 16 | byteL) / 10).toFixed(1));
    return (value > 2000 || value < -2000) ? 0 : value;
  }

  if (port === 2) {
    data.battery_volt_abs = (bytes[0] << 8 | bytes[1]) / 1000;
		data.battery_state_abs = mapBatteryVoltageAbs(data.battery_volt_abs);
    data["temperature_degrC_abs@channel1"] = toTemperature(bytes[3], bytes[4]);
    data["temperature_degrC_abs@channel2"] = toTemperature(bytes[5], bytes[6]);
    return { data };
  }

  return {
    errors: ["Unsupported FPort"]
  };
}
```