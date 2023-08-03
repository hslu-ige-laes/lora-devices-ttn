---
layout: default
title: netvox - R718N37
parent: Sensors
---

<img src="https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/netvox-r871n37.png" width="250" align="right" class="inline"/>

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
- Size
  - Body: 112 × 65 × 32 mm
	- Sensors: 42.5 × 27.5 × 25 mm
- Weight
  - Body: ca. 141g
	- Sensors: ca. 50g * 3
	- Total: ca. 291g
Operating Temperature Range: -20°C ... 55°C

---

## Documents
  - [Datasheet (2020-11-25)](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/netvox-r871n37_02.pdf)
  - [User Manual (2023-03-01)](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/netvox-r871n37_02.pdf)

---

## Ordering Info
- [Ordering Link](https://iot-shop.de/shop/nv-r718n37-netvox-r718n37-3-phasen-stromsensor-75a-4285#attr=488,489,490,496,487,491,492,493,494,495)

---

## Adding the Device to TTN
### Handler device information
- The supplier sent us a csv file with the `DevEUI`, `AppEUI` and `AppKey`

### Device Registration
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
- The payload formatter should already be preset. If not, you can copy/paste it

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