---
layout: default
title: Dragino - CS01-LB
parent: Sensors
---

<img src="https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/dragino-cs01-lb_01.jpg" width="250" align="right">

# Dragino - CS01-LB
{: .no_toc }

- Manufacturer: <a href="https://www.dragino.com/" target="_blank">Dragino</a>
- Product: <a href="https://www.dragino.com/products/distance-level-sensor/item/322-cs01-lb.html" target="_blank">CS01-LB</a>

The CS01-LB is a LoRaWAN 4 Channel Current Sensor Converter. It can be used to monitor the machine running states and analyze power consumption trends.
The current reading cannot be used for power calculations. But with machines running different stages, the stage can get identified with the current signal.
The current sensors are detachable and can be replaced with different scales.
---

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Specifications

- indoor/outdoor device
- Price ca. CHF 79.- without sensors (19.08.2025)
- 4 External Sensors, each ca. CHF 17.00
  - **SCT013G-D-100 Current Converter 100A:50mA**
- Power Supply: 1 built in 8500mAh Li-SOCI2 battery
  - Expected life time: depending on usage, 5 ... 10 years
- LoRaWAN version: 1.0.3
- LoRaWAN device class: A
- Protection: [IP66](https://en.wikipedia.org/wiki/IP_Code)
- Size: 145 x 102 x 51 mm
- Weight: 308 g

---

## Documents/Links
- [Datasheet from dragino.com (2023-08-09)](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/dragino-cs01-lb_02.pdf)
- [User Manual (online)](https://wiki.dragino.com/xwiki/bin/view/Main/User%20Manual%20for%20LoRaWAN%20End%20Nodes/CS01-LB_LoRaWAN_4_Channels_Current_Sensor_Converter_User_Manual/)
- [User Manual (pdf, 2025-02-04)](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/dragino-cs01-lb_03.pdf)

---

## Ordering Info
- Part Number: CS01-LB-EU868
- [Ordering Link CS01-LB](https://www.bastelgarage.ch/lsn50v2-d23-lorawan-3-kanal-temperatursensor-node-868mhz?search=lsn50%20v2)
  - Attention: Current clamps are not included in the scope of delivery and must be purchased separately.
- [Ordering Link SCT013G-D-100](https://www.bastelgarage.ch/sct013g-d-100-stromwandler-100a-50ma?search=Dragino%20Stromwandler)

---

## Device specific Information

### Push button

| Behavior on ACT | Function | Action |
|---|---|---|
| Pressing ACT between 1s and 3s | Send an uplink | If the sensor is already joined to the LoRaWAN network, it will send an uplink packet; blue LED will blink once. <br>Meanwhile, the BLE module will be active and the user can connect via BLE to configure the device. |
| Pressing ACT for more than 3s | Activate Device | Green LED will fast blink 5 times, device enters OTA mode for 3 seconds, then starts to join the LoRaWAN network. <br>Green LED will turn solid for 5 seconds after joining the network. <br>Once the sensor is active, the BLE module will be active and the user can connect via BLE to configure the device, whether it has joined the network or not. |
| Fast press ACT 5 times | Deactivate Device | Red LED will stay solid for 5 seconds, indicating the device is in Deep Sleep Mode. |

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
10. Switch on the device

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
function mapBatteryVoltage(voltage) {
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
  var port = input.fPort;
  var bytes = input.bytes;
  var data = {};

  switch (port) {
    case 0x02:
    case 2:
      // CS01-LB Main Data
      var value = (bytes[0] << 8 | bytes[1]) & 0x3FFF;
      data.battery_volt_abs = value / 1000;
      data.battery_state_abs = mapBatteryVoltage(data.battery_volt_abs);
      data["current_ampere_abs@channelA"] = parseFloat(((bytes[2] << 8 | bytes[3]) / 100).toFixed(2));
      data["current_ampere_abs@channelB"] = parseFloat(((bytes[4] << 8 | bytes[5]) / 100).toFixed(2));
      data["current_ampere_abs@channelC"] = parseFloat(((bytes[6] << 8 | bytes[7]) / 100).toFixed(2));
      data["current_ampere_abs@channelN"] = parseFloat(((bytes[8] << 8 | bytes[9]) / 100).toFixed(2));

      if (bytes.length == 11) {
        return {
          data: data,
        };
      }
      break;

    case 0x05:
    case 5:      
      data.battery_volt_abs = (bytes[5] << 8 | bytes[6]) / 1000;
      data.battery_state_abs = mapBatteryVoltage(data.battery_volt_abs);

      return {
        data: data,
      };

    default:
      return {
        errors: ["unknown or unsupported FPort"]
      };
  }
}
```