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
- [IP55](https://en.wikipedia.org/wiki/IP_Code) device
- Price ca. CHF 183.- (15.07.2020)
- Up to 500 pulses/second Flash LED rate counting
- Cable length approx. 0.5m
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

## Tested with following meters
- **Landis & Gyr ZMD120AP** (1'000 impulses per kWh)<br>
  <img src="https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/nke-watteco-flash-o_04.png" width="250"><br>
  - Place the sensor over the LED (yellow colored)
- **Kamstrup DK-8660 Omnipower** (1'000 impulses per kWh)<br>
  <img src="https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/nke-watteco-flash-o_05.png" width="250"><br>
  - Place the sensor over the LED (yellow colored)
---

## Adding the Device to TTN
### Handler device information

> The supplier should give you a csv file where you can find the `DevEUI`, `AppEUI` and `AppKey`

### Device Registration

> Before a device can communicate via "The Things Network" we need to register it with an application.<br>
> The nke WATTECO sensors use the so called "Over The Air Activation" (OTAA) and for a secure communication we will need to register the beforehand mentioned keys.

1. [Log in](https://console.thethingsnetwork.org/applications) and `open the application` to which you wish to add a device
2. Under `Settings > EUIs`  click `(+) add EUI`
3. Click the `pen symbol` and paste the before mentioned `AppEUI` from the csv file and hit the button `Add EUI`
4. Click `Devices > register device`
   - For `Device ID`, choose a unique ID of lower case, alphanumeric characters and nonconsecutive - and _.
   - For `Device EUI` paste the copied "`DevEUI from the csv file`
   - For `App Key` press the `pen icon` and paste the `AppKey from the csv file`
   - Select the correct `App EUI` which we entered in step 3
   - press the button `Register`
5. Now we have to reset the device manually by switching the small button under the cover from `Off` to `On`
6. The device should log in and you should see a green circle as `Status` in the tab `Device Overview`.
   - if not, please wait several hours and check again. The first attempt might take a while.

### Device Configuration

> Now you can see the incoming telegrams in the tab Data, but their content, the payload, is cryptic...!<br>
> We need to tell the "The Things Network" where to find e.g. the counter value in these cryptic numbers and letters. We can do that with configuring a "Payload Decoder Function".

1. [Log in](https://console.thethingsnetwork.org/applications) and open the `application`
2. Select the tab `Payload Formats > decoder` and copy/paste the following code from [nke-WATTECO](http://support.nke-watteco.com/wp-content/uploads/2020/02/decodeZCL_svn5139.js):
	
```javascript
function Decoder(bytes, port) {
  var decoded = {};
  var index = 7;
  
	if (port === 125) 
	{
	  decoded.counter = (bytes[index]*256*256*256+bytes[index+1]*256*256+bytes[index+2]*256+bytes[index+3]);
	
	}
  return decoded;
}
```
	
3. Copy/Paste the following test payload into the field `Payload` and press `Test`

`110A000F04022300000DF5`

> Attention: all incoming and outgoing frames have to be sent on port 125
		
4. You should see the following result
	
```json
{
  "counter": 3573
}
```
	
	
<b>counter</b> -> Counter [impulses]<br>
Attention, the amount of energy or water flow per impulse is meter specific. Electricity meters have e.g. 1'000 impulses per kWh.

5. Press `save payload functions`

> Now you should be able to see the decoded data of your sensor in the tab `Data`.<br>
> Trigger a new telegram by pressing the small button.<br><br>

### Change measurement interval
> The Flash'O sends per default a telegram once a day with the counter value.<br>
> If you want to change that you have to send a telegram to the device. To do so follow the instructions below.<br>
> There are two properties of the Flash'O, the min sending interval in seconds and the impuls count.<br>

1. [Log in](https://console.thethingsnetwork.org/applications) and open the `application`
2. Select the device in the tab `Devices` and scroll down to `Downlink`
3. Copy/paste one of the following payloads into the big field, change the `FPort to 125` and press `Send`
   - every 15 minutes and every 200 impulses: `11 06 00 0f 00 04 02 23 00 00 03 84 00 00 00 c8`
   - every 1 day: `11 06 00 0f 00 04 02 23 00 00 00 00 00 00 00 00`
4. In the `Data` tab you should now see the scheduled telegram. The next time the device is sending data a short timeframe for the downlink-message will open and the telegram gets sent. This can be accelerated by pressing the key on the nkewattecoflasho and forcing a telegram to be sent.

> For details and other configurations see [Frame Examples](http://support.nke-watteco.com/flasho/#FrameExamples)

### Add Storage
> Normally incoming data in the network only gets forwarded to the end user applications and does not get saved.<br>
> "The Things Network" offers a seven day storage which is accessible through an API. To activate it, follow these steps.

1. [Log in](https://console.thethingsnetwork.org/applications) and open the `application`
2. Select the tab `Integrations > (+) add integration`
3. Select `Data Storage` and press `Add integration`

> Now we're done with the configuration :-)
