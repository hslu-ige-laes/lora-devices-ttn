---
layout: default
title: netvox - R718N37
parent: Sensors
---

<img src="https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/netvox-r871n37_01.jpg" width="250" align="right" class="inline"/>

# netvox - R718N37
{: .no_toc }

- Manufacturer: <a href="http://www.netvox.com.tw/" target="_blank">netvox</a>
- Product: <a href="http://www.netvox.com.tw/product.asp?pro=R718N37" target="_blank">R718N37</a>

The R718N37 is a 3-Phase Current Meter with 3 x 75A Clamp-On Current Transformer.

---

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Specifications
- Price ca. CHF 189.- (03.08.2023)
- Cable length approx. 0.8m
- Protection: [Body IP53](https://en.wikipedia.org/wiki/IP_Code), [Sensor IP30](https://en.wikipedia.org/wiki/IP_Code) 
- Power Supply: 2 batteries, 3.6 V, 3600mAh, saft Li-SOCl2
  - Expected life time: not available
- Low Voltage Threshold: 3.2V
- Size: Body: 112 × 65 × 32 mm, Sensors: 42.5 × 27.5 × 25 mm
- Weight: Body: ca. 141g, Sensors: ca. 50g * 3, Total: ca. 291g
- Operating Temperature Range: -20°C ... 55°C

---

## Documents
  - [Datasheet (2020-11-25)](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/netvox-r871n37_02.pdf)
  - [User Manual (2023-03-01)](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/netvox-r871n37_02.pdf)

---

## Ordering Info
- [Ordering Link](https://iot-shop.de/shop/nv-r718n37-netvox-r718n37-3-phasen-stromsensor-75a-4285#attr=488,489,490,496,487,491,492,493,494,495)

---
## Device specific Information

### Handler device information
- The supplier Alliot sent us a pdf file with a default `AppEUI` and `AppKey`. The `DevEUI` is printed on a sticker on the devise.
- **It's important to change the AppEUI and AppKey for security reasons**, see chapter below for detailed instructions...

### Change the default AppEUI and AppKey
The seller Alliot provisioned the device with default `AppEUI` and `AppKey`. 

1. Add the sensor to your LoRaWAN platform using the `DevEUI` on the box or device label and the following App settings
  - AppEUI: `70B3D57ED001148C`
  - AppKey: `D9DE989A000F4D590AD6CABC0F98500C`

2. Power on the sensor (insert batteries and hold the button until the LED lights up). Observe uplink messages coming from the sensor to your Network Server to confirm it is operating.

3. Send a downlink message using fport 8 to the sensor to re-program the AppEUI. The first byte is 03, then the new AppEUI, then 8 bytes of zero padding.
  - e.g. new AppEUI: `40B3D37ED0027GFB`
  - HEX payload to send on fPort 8: `0340B3D37ED0027GFB0000000000000000`

4. Once the sensor receives this it will respond with an uplink of:
  - 8300000000000000000000000000000000 -> OK
  - 8301000000000000000000000000000000 -> not successful
    - If unsuccessful, try resending

5. Send a downlink to change the AppKey, also on fport 8. The first byte is 05, followed by the new AppKey.
  - e.g. new AppKey: `4F27583D49C434AA02F3F67F85573C9A`
  - HEX payload to send on fport 8: `054F27583D49C434AA02F3F67F85573C9A`

6. The sensor will respond with:
  - 8500000000000000000000000000000000 -> OK
  - 8501000000000000000000000000000000 -> not successful
    - If unsuccessful, try resending

7. Reset the sensor by holding down the button(s) on it for around 10 seconds until the LED starts to flash.
- It will flash 20 times in total
- You can release the button once it starts flashing.
- Once it has stopped flashing, you also have to remove the batteries for at least 30 seconds to ensure it has powered down fully.

8. Re-insert the batteries and power the sensor up by holding the button and it will attempt to Join using the new AppEUI and AppKey.

9. Delete the device and create the new one according to chapter [Adding the Device to TTN](https://hslu-ige-laes.github.io/lora-devices-ttn/docs/sensors/netvox-R718N37/#adding-the-device-to-ttn)

### On/Off
- Power on: Insert batteries
- Turn on: Press and hold the function key for 3 seconds till the green indicator flashes once.
- Turn off (Restore to factory setting): Press and hold the function key for 5 seconds till green indicator flashes 20 times.
- Power off: Remove Batteries
<br><br>
**Note**<br>
- The device will be off in default after removing the battery and insert it again.
- It is suggested to wait for at least 10 seconds between turning the device on and off.

### Network Joining
**Device never joined a network**
- Turn on the device, and it will search for the network to join.
- The green indicator light stays on for 5 seconds: joins the network successfully
- The green indicator light remains off: fail to join the network

**Device had joined already a network**
- Turn on the device, and it will search for the previous network to join.
- The green indicator light stays on for 5 seconds: joins the network successfully
- The green indicator light remains off: fail to join the network

**Fail to Join the Network**
  - Suggest to check the device verification information on the gateway

---

## Adding the Device to TTN
- Before a device can communicate via "The Things Network" we have to add it to an application.<br>

1. [Create a new application](https://hslu-ige-laes.github.io/lora-devices-ttn/docs/getting_started#create-a-new-application)
2. Under `Overview` click `(+) Register device`
3. Under `Input method` select `Select the end device in the LoRaWAN Device Repository`
4. Enter the following device information
   - `End device brand` select `Netvox Technology Co., Ltd`
   - `Model` select `R718N3`
   - `Hardware Ver.` select `2`
   - `Firmware` select `10`
	 - `Profile (Region)` select `EU_863_870`
5. Under `Frequency plan` select `Europe 863-870 Mhz (SF9 for RX2 - recommended)`
6. As `JoinEUI` enter the `App EUI`, fill in as well the `DevEUI` and the `AppKey`
7. Set an end-device name
8. Press `Register end device`
9. Open the case and take out the batteries
10. Replace the batteries and close the case

- Now the device should join the network and you can see the incoming telegrams in the `Live data` section
- The payload formatter should already be preset. If not, you can copy/paste it from below

---

## Payload formatter

```javascript
function getCfgCmd(cfgcmd){
  var cfgcmdlist = {
    1:   "ConfigReportReq",
    129: "ConfigReportRsp",
    2:   "ReadConfigReportReq",
    130: "ReadConfigReportRsp"
  };
  return cfgcmdlist[cfgcmd];
}

function getCmdToID(cmdtype){
  if (cmdtype == "ConfigReportReq")
	  return 1;
  else if (cmdtype == "ConfigReportRsp")
	  return 129;
  else if (cmdtype == "ReadConfigReportReq")
	  return 2;
  else if (cmdtype == "ReadConfigReportRsp")
	  return 130;
}

function getDeviceName(dev){
  var deviceName = {
	74: "R718N3"
  };
  return deviceName[dev];
}

function getDeviceID(devName){
  if (devName == "R718N3")
	  return 74;
}

function padLeft(str, len) {
    str = '' + str;
    if (str.length >= len) {
        return str;
    } else {
        return padLeft("0" + str, len);
    }
}

function decodeUplink(input) {
  var data = {};
  switch (input.fPort) {
    case 6:
		if (input.bytes[2] === 0x00)
		{
			data.Device = getDeviceName(input.bytes[1]);
			data.SWver =  input.bytes[3]/10;
			data.HWver =  input.bytes[4];
			data.Datecode = padLeft(input.bytes[5].toString(16), 2) + padLeft(input.bytes[6].toString(16), 2) + padLeft(input.bytes[7].toString(16), 2) + padLeft(input.bytes[8].toString(16), 2);
			
			return {
				data: data,
			};
		}
		
		data.Device = getDeviceName(input.bytes[1]);
		if (input.bytes[3] & 0x80)
		{
			var tmp_v = input.bytes[3] & 0x7F;
			data.Volt = (tmp_v / 10).toString() + '(low battery)';
		}
		else
			data.Volt = input.bytes[3]/10;

		if (input.bytes[2] === 0x01)
		{
			data.Current1 = (input.bytes[4]<<8 | input.bytes[5]);
			data.Current2 = (input.bytes[6]<<8 | input.bytes[7]);
			data.Current3 = (input.bytes[8]<<8 | input.bytes[9]);
			data.Multiplier1 = input.bytes[10];
		}
		else if (input.bytes[2] === 0x02)
		{	
			data.Multiplier2 = input.bytes[4];
			data.Multiplier3 = input.bytes[5];
		}
		break;
		
	case 7:
		data.Cmd = getCfgCmd(input.bytes[0]);
		data.Device = getDeviceName(input.bytes[1]);
		if (input.bytes[0] === getCmdToID("ConfigReportRsp"))
		{
			data.Status = (input.bytes[2] === 0x00) ? 'Success' : 'Failure';
		}
		else if (input.bytes[0] === getCmdToID("ReadConfigReportRsp"))
		{
			data.MinTime = (input.bytes[2]<<8 | input.bytes[3]);
			data.MaxTime = (input.bytes[4]<<8 | input.bytes[5]);
			data.CurrentChange = (input.bytes[6]<<8 | input.bytes[7]);
		}
		
		break;	

	default:
      return {
        errors: ['unknown FPort'],
      };
	  
    }
          
	 return {
		data: data,
	};
 }
  
function encodeDownlink(input) {
  var ret = [];
  var devid;
  var getCmdID;
	  
  getCmdID = getCmdToID(input.data.Cmd);
  devid = getDeviceID(input.data.Device);

  if (input.data.Cmd == "ConfigReportReq")
  {
	  var mint = input.data.MinTime;
	  var maxt = input.data.MaxTime;
	  var currentChg = input.data.CurrentChange;
	  
	  ret = ret.concat(getCmdID, devid, (mint >> 8), (mint & 0xFF), (maxt >> 8), (maxt & 0xFF), (currentChg >> 8), (currentChg & 0xFF), 0x00, 0x00, 0x00);
  }
  else if (input.data.Cmd == "ReadConfigReportReq")
  {
	  ret = ret.concat(getCmdID, devid, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00);
  }  
  
  return {
    fPort: 7,
    bytes: ret
  };
}  
  
function decodeDownlink(input) {
  var data = {};
  switch (input.fPort) {
    case 7:
		data.Cmd = getCfgCmd(input.bytes[0]);
		data.Device = getDeviceName(input.bytes[1]);
		if (input.bytes[0] === getCmdToID("ConfigReportReq"))
		{
			data.MinTime = (input.bytes[2]<<8 | input.bytes[3]);
			data.MaxTime = (input.bytes[4]<<8 | input.bytes[5]);
			data.CurrentChange = (input.bytes[6]<<8 | input.bytes[7]);
		}

		break;
		
    default:
      return {
        errors: ['invalid FPort'],
      };
  }
  
  return {
		data: data,
	};
}
```