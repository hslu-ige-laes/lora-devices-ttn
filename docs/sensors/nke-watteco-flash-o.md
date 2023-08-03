---
layout: default
title: nke WATTECO - Flash O
parent: Sensors
---

<img src="https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/nke-watteco-flash-o_01.png" width="250" align="right" class="inline"/>

# nke WATTECO - Flash O
{: .no_toc }

- Manufacturer: <a href="http://www.nke-watteco.com/" target="_blank">nke WATTECO</a>
- Product: <a href="http://www.nke-watteco.com/product/flasho/" target="_blank">Flash'O</a>

The Flash'O detects the impulses of flashin LED's from pulse meters: e.g. water, gas, electricity or energy meters.

---

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Specifications
- Price ca. CHF 183.- (15.07.2020)
- Up to 500 pulses/second Flash LED rate counting
- Cable length approx. 0.5m
- Protection: [IP55](https://en.wikipedia.org/wiki/IP_Code)
- Power Supply: 1 battery, 3.6 V, 3600mAh, Lithium
  - Expected life time: up to 12 years with 1 telegram per day
- Size: 84 × 82 × 85 mm

---

## Documents
  - [Payload description from support.nke-watteco.com (2020-07-15)](http://support.nke-watteco.com/flasho/#HumanMachineInterface)
  - [Datasheet from nke-watteco.fr (2020-07-15)](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/nke-watteco-flash-o_02.pdf)

---

## Ordering Info
- [Ordering Link](https://smartmakers.io/en/iot-sensors-devices-overview/)
- Product Ref 50-70-071 (FLASH’O with sensor - mounted in factory with 0.5m cable)

---

## Compatibility of Electricity Meters
The device was successfully tested with the following products.

- **Landis & Gyr ZMD120AP** (1'000 impulses per kWh)<br>
  <img src="https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/nke-watteco-flash-o_04.png" width="250"><br>
  - Place the sensor over the LED (yellow colored)

- **Kamstrup DK-8660 Omnipower** (1'000 impulses per kWh)<br>
  <img src="https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/nke-watteco-flash-o_05.png" width="250"><br>
  - Place the sensor over the LED (yellow colored)
	
---

## Device specific Information
### Handler device information

- The supplier should give you a csv file where you can find the `DevEUI`, `AppEUI` and `AppKey`

---

## Adding the Device to TTN
- Before a device can communicate via "The Things Network" we have to add it to an application.<br>

1. [Create a new application](https://hslu-ige-laes.github.io/lora-devices-ttn/docs/getting_started#create-a-new-application)
2. Under `Overview` click `(+) Register device`
3. Under `Input method` select `Select the end device in the LoRaWAN Device Repository`
4. Enter the following device information
   - `End device brand` select `nke WATTECO`
   - `Model` select `Flash'O`
   - `Hardware Ver.` select `50-70-071` or whatever the sticker says
   - `Firmware` select `3.5.2` or whatever the sticker says
	 - `Profile (Region)` select `EU_863_870`
5. Under `Frequency plan` select `Europe 863-870 Mhz (SF9 for RX2 - recommended)`
6. Under `JoinEUI` enter the `App EUI`
7. Enter as well the `DevEUI` and the `AppKey`
8. Set an end-device name
9. Press `Register end device`
10. Now we have to reset the device manually by switching the small button under the cover from `Off` to `On`
11. The device should log in and you should see a green circle as `Status` in the tab `Device Overview`.
   - if not, please wait several hours and check again. The first attempt might take a while.

- Now the device should join the network and you can see the incoming telegrams in the `Live data` section
- The payload formatter should already be preset. If not, you can copy/paste it from below

---

## Optional settings
### Change measurement interval
- The Flash'O sends per default a telegram once a day with the counter value.<br>
- If you want to change that you have to send a telegram to the device. To do so follow the instructions below.<br>
- There are two properties of the Flash'O, the min sending interval in seconds and the impuls count.<br>

1. Select the device and change to the tab `Messaging`, select `Downlink`
2. Change the `FPort to 125`
2. Copy/paste one of the following payloads into the `Payload` field
   - every 15 minutes and/or every 200 impulses: `11 06 00 0f 00 04 02 23 00 00 03 84 00 00 00 c8`
   - every 1 day: `11 06 00 0f 00 04 02 23 00 00 00 00 00 00 00 00`
3. Press `Send`
4. In the `Data` tab you should now see the scheduled telegram. The next time the device is sending data, a short timeframe for the downlink-message will open and the telegram gets sent. This can be accelerated by pressing the key on the nkewattecoflasho and forcing a telegram to be sent.

- For details and other configurations see [Frame Examples](http://support.nke-watteco.com/flasho/#FrameExamples)

---

## Payload formatter

```javascript

```
