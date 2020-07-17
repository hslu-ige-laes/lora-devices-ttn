---
layout: default
title: nke WATTECO - Flash O
parent: Sensors
---

# nke WATTECO - Flash O

<img src="https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/nke-watteco-flash-o_01.png" width="250" align="right" class="inline"/>

- Manufacturer: <a href="http://www.nke-watteco.com/" target="_blank">nke WATTECO</a>
- Product: <a href="http://www.nke-watteco.com/product/flasho/" target="_blank">Flash'O</a>

> The Flash'O detects the impulses of flashin LED's from pulse meters: e.g. water, gas, electricity or energy meters.

## Specifications
- [IP55](https://en.wikipedia.org/wiki/IP_Code) device
- Price ca. CHF 183.- (15.07.2020)
- Up to 500 pulses/second Flash LED rate counting
- Cable length approx. 0.5m
- Power Supply: 1 battery, 3.6 V, 3600mAh, Lithium
  - Expected life time: up to 12 years with 1 telegram per day
- Size: 84 × 82 × 85 mm

## Documents
  - [Payload description from support.nke-watteco.com (2020-07-15)](http://support.nke-watteco.com/flasho/#HumanMachineInterface)
  - [Datasheet from nke-watteco.fr (2020-07-15)](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/nke-watteco-flash-o_02.pdf)

## Ordering info
- [Ordering Link](https://smartmakers.io/en/iot-sensors-devices-overview/)
- Product Ref 50-70-071 (FLASH’O with sensor - mounted in factory with 0.5m cable)

## Tested with following meters
- Landis & Gyr ZMD120AP (1'000 impulses per kWh)<br>
<img src="https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/nke-watteco-flash-o_04.png" width="250"> -> Place the sensor over the LED (yellow colored)</img>

## Device Configuration
### 1. Create an Account
> To register your device you’ll need a "The Things Network" account.<br>
> If you don't have one already, please follow these steps:

1. Go to [account.thethingsnetwork.org](https://account.thethingsnetwork.org/) and [click create an account](https://account.thethingsnetwork.org/register)
   - You will receive an email to confirm your email address. You have 24 hours to do so.
2. Select [Console](https://console.thethingsnetwork.org/) from the menue on top right
3. From the top right `menu`, select `your name > Settings`. Then change the `default Handler` if the one currently selected is not where you’ll be deploying most of your devices.

### 2. Add an Application

> To keep the payload decoder as simple as possible it is recommended to create an own application only for the nke watteco sensors. You can add multiple sensors from nke-watteco of different types to the application.

1. Log in and open the [Console > Application](https://console.thethingsnetwork.org/).
2. In the console, click [(+) add application](https://console.thethingsnetwork.org/applications/add)
   - For `Application ID`, choose a unique ID of lower case, alphanumeric characters and nonconsecutive - and _.
   - For `Application Description`, enter anything you like.

<img src="https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/nke-watteco-flash-o_03.png" width="700"></img><br>

3. Click `Add Application` to finish.

> You will be redirected to the newly added Application page.

### 3. Handler device information

> The supplier should give you a csv file where you can find the `DevEUI`, `AppEUI` and `AppKey`

### 4. Device Registration

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

### 5. Device Configuration

> Now you can see the incoming telegrams in the tab Data, but their content, the payload, is cryptic...!<br>
> We need to tell the "The Things Network" where to find e.g. the counter value in these cryptic numbers and letters. We can do that with configuring a "Payload Decoder Function".

1. [Log in](https://console.thethingsnetwork.org/applications) and open the `application`
2. Select the tab `Payload Formats > decoder` and copy/paste the following code from [nke-WATTECO](http://support.nke-watteco.com/wp-content/uploads/2020/02/decodeZCL_svn5139.js):

```javascript
/*
 * A command line script to decode a ZCL payload at a given port
 * Example:
 *
 *     node decodeZCL.js 125 110A04020000290998
 */
 
// ----------------------------------------------------------------
// ----------------------- FUNCTIONS PART -------------------------
// ----------------------------------------------------------------

function UintToInt(Uint, Size) {
    if (Size === 2) {
      if ((Uint & 0x8000) > 0) {
        Uint = Uint - 0x10000;
      }
    }
    if (Size === 3) {
      if ((Uint & 0x800000) > 0) {
        Uint = Uint - 0x1000000;
      }
    }
    if (Size === 4) {
      if ((Uint & 0x80000000) > 0) {
        Uint = Uint - 0x100000000;
      }
    }


    return Uint;
}

function decimalToHex(d, padding) {
    var hex = Number(d).toString(16).toUpperCase();
    padding = typeof (padding) === "undefined" || padding === null ? padding = 2 : padding;

    while (hex.length < padding) {
        hex = "0" + hex;
    }

    return "0x" + hex;
}

function Bytes2Float32(bytes) {
    var sign = (bytes & 0x80000000) ? -1 : 1;
    var exponent = ((bytes >> 23) & 0xFF) - 127;
    var significand = (bytes & ~(-1 << 23));

    if (exponent == 128) 
        return sign * ((significand) ? Number.NaN : Number.POSITIVE_INFINITY);

    if (exponent == -127) {
        if (significand == 0) return sign * 0.0;
        exponent = -126;
        significand /= (1 << 22);
    } else significand = (significand | (1 << 23)) / (1 << 23);

    return sign * significand * Math.pow(2, exponent);
}

function parseHexString(str) { 
    var result = [];
    while (str.length >= 2) { 
        result.push(parseInt(str.substring(0, 2), 16));

        str = str.substring(2, str.length);
    }

    return result;
}

function Decoder(bytes, port) {
  // Decode an uplink message from a buffer
  // (array) of bytes to an object of fields.
	var decoded = {};
	decoded.lora = {};

	decoded.lora.port  = port;
	
	// Get raw payload
	var bytes_len_	= bytes.length;
	var temp_hex_str = ""

	decoded.lora.payload  = "";

	for( var j = 0; j < bytes_len_; j++ )

	{
		temp_hex_str   = bytes[j].toString( 16 ).toUpperCase( );
		if( temp_hex_str.length == 1 )
		{
		  temp_hex_str = "0" + temp_hex_str;
		}
		decoded.lora.payload += temp_hex_str;
		var date = new Date();
		decoded.lora.date = date.toISOString();
	}
	
	if (port === 125) 
	{
		//batch
		batch = !(bytes[0] & 0x01);
	
		//trame standard
		if (batch === false){
			decoded.zclheader = {};
			decoded.zclheader.report =  "standard";
			attributID = -1;
			cmdID = -1;
			clusterdID = -1;
			//endpoint
			decoded.zclheader.endpoint = ((bytes[0]&0xE0)>>5) | ((bytes[0]&0x06)<<2);
			//command ID
			cmdID =  bytes[1]; decoded.zclheader.cmdID = decimalToHex(cmdID,2);
			//Cluster ID
			clusterdID = bytes[2]*256 + bytes[3]; decoded.zclheader.clusterdID = decimalToHex(clusterdID,4);
			
			// decode report and read atrtribut response
			if((cmdID === 0x0a)|(cmdID === 0x8a)|(cmdID === 0x01)){
				decoded.data = {};
				//Attribut ID 
				attributID = bytes[4]*256 + bytes[5];decoded.zclheader.attributID = decimalToHex(attributID,4);
				
				if (cmdID === 0x8a) decoded.zclheader.alarm = 1;
				//data index start
				if ((cmdID === 0x0a) | (cmdID === 0x8a))	index = 7;
				if (cmdID === 0x01)	{index = 8; decoded.zclheader.status = bytes[6];}
				
				//temperature
				if (  (clusterdID === 0x0402 ) & (attributID === 0x0000)) decoded.data.temperature = (UintToInt(bytes[index]*256+bytes[index+1],2))/100;
				//humidity
				if (  (clusterdID === 0x0405 ) & (attributID === 0x0000)) decoded.data.humidity = (bytes[index]*256+bytes[index+1])/100;
				//binary input counter
				if (  (clusterdID === 0x000f ) & (attributID === 0x0402)) decoded.data.counter = (bytes[index]*256*256*256+bytes[index+1]*256*256+bytes[index+2]*256+bytes[index+3]);
				// binary input present value
				if (  (clusterdID === 0x000f ) & (attributID === 0x0055)) decoded.data.pin_state = !(!bytes[index]);
				//multistate output
				if (  (clusterdID === 0x0013 ) & (attributID === 0x0055)) decoded.data.value = bytes[index];
				// on/off present value
				if (  (clusterdID === 0x0006 ) & (attributID === 0x0000)) {state = bytes[index]; if(state === 1) decoded.data.state = "ON"; else decoded.data.state = "OFF" ; }
				// multibinary input present value
				if (  (clusterdID === 0x8005 ) & (attributID === 0x0000)) 
				{
					decoded.data.pin_state_1 = ((bytes[index+1]&0x01) === 0x01);
					decoded.data.pin_state_2 = ((bytes[index+1]&0x02) === 0x02);
					decoded.data.pin_state_3 = ((bytes[index+1]&0x04) === 0x04);
					decoded.data.pin_state_4 = ((bytes[index+1]&0x08) === 0x08);
					decoded.data.pin_state_5 = ((bytes[index+1]&0x10) === 0x10);
					decoded.data.pin_state_6 = ((bytes[index+1]&0x20) === 0x20);
					decoded.data.pin_state_7 = ((bytes[index+1]&0x40) === 0x40);
					decoded.data.pin_state_8 = ((bytes[index+1]&0x80) === 0x80);
					decoded.data.pin_state_9 = ((bytes[index]&0x01) === 0x01);
					decoded.data.pin_state_10 = ((bytes[index]&0x02) === 0x02);
				}
				//analog input
				if (  (clusterdID === 0x000c ) & (attributID === 0x0055)) decoded.data.analog = Bytes2Float32(bytes[index]*256*256*256+bytes[index+1]*256*256+bytes[index+2]*256+bytes[index+3]);

				//modbus 
				if (  (clusterdID === 0x8007 ) & (attributID === 0x0001)) 
				{
					decoded.data.modbus_payload = "";
					decoded.data.modbus_size = bytes[index]; 
					for( var j = 0; j < decoded.data.modbus_size -1; j++ )
					{
						temp_hex_str   = bytes[index+j+1].toString( 16 ).toUpperCase( );
						if( temp_hex_str.length == 1 )
						{
							temp_hex_str = "0" + temp_hex_str;
						}
						decoded.data.modbus_payload += temp_hex_str;
					}
				}
				
				//multimodbus 
				if (  (clusterdID === 0x8009 ) & (attributID === 0x0000)) 
				{
					decoded.data.multimodbus_payloads = "";
					decoded.data.multimodbus_size = bytes[index]; 
					decoded.data.multimodbus_frame_series_sent = bytes[index+1];
					decoded.data.multimodbus_frame_number_in_serie = (bytes[index+2] & 0xE0) >> 5; 
					decoded.data.multimodbus_last_frame_of_serie = (bytes[index+2] & 0x1C ) >> 2; 
					decoded.data.multimodbus_EP9 = ((bytes[index+2]&0x01) === 0x01);
					decoded.data.multimodbus_EP8 = ((bytes[index+2]&0x02) === 0x02);
					decoded.data.multimodbus_EP7 = ((bytes[index+3]&0x80) === 0x80);
					decoded.data.multimodbus_EP6 = ((bytes[index+3]&0x40) === 0x40);
					decoded.data.multimodbus_EP5 = ((bytes[index+3]&0x20) === 0x20);
					decoded.data.multimodbus_EP4 = ((bytes[index+3]&0x10) === 0x10);
					decoded.data.multimodbus_EP3 = ((bytes[index+3]&0x08) === 0x08);
					decoded.data.multimodbus_EP2 = ((bytes[index+3]&0x04) === 0x04);
					decoded.data.multimodbus_EP1 = ((bytes[index+3]&0x02) === 0x02);
					decoded.data.multimodbus_EP0 = ((bytes[index+3]&0x01) === 0x01); 
					index2 = index + 4;
					if (decoded.data.multimodbus_EP0 === true)
					{
						decoded.data.multimodbus_EP0_slaveID = bytes[index2];
						index2 = index2 + 1;
						decoded.data.multimodbus_EP0_fnctID = bytes[index2];
						index2 = index2 + 1;
						decoded.data.multimodbus_EP0_datasize = bytes[index2];
						index2 = index2 + 1;
						decoded.data.multimodbus_EP0_payload = ""
						if (bytes[index2] === undefined ) return decoded;
						for( var j = 0; j < decoded.data.multimodbus_EP0_datasize; j++ )
						{
							temp_hex_str   = bytes[index2+j].toString( 16 ).toUpperCase( );
							if( temp_hex_str.length == 1 )
							{
								temp_hex_str = "0" + temp_hex_str;
							}
							decoded.data.multimodbus_EP0_payload += temp_hex_str;
						}
						index2 = index2 + decoded.data.multimodbus_EP0_datasize;
					}
					if (decoded.data.multimodbus_EP1 === true)
					{
						decoded.data.multimodbus_EP1_slaveID = bytes[index2];
						index2 = index2 + 1;
						decoded.data.multimodbus_EP1_fnctID = bytes[index2];
						index2 = index2 + 1;
						decoded.data.multimodbus_EP1_datasize = bytes[index2];
						index2 = index2 + 1;
						decoded.data.multimodbus_EP1_payload = ""
						if (bytes[index2] === undefined ) return decoded;
						for( var j = 0; j < decoded.data.multimodbus_EP1_datasize; j++ )
						{
							temp_hex_str   = bytes[index2+j].toString( 16 ).toUpperCase( );
							if( temp_hex_str.length == 1 )
							{
								temp_hex_str = "0" + temp_hex_str;
							}
							decoded.data.multimodbus_EP1_payload += temp_hex_str;
						}
						index2 = index2 + decoded.data.multimodbus_EP1_datasize;
					}
					if (decoded.data.multimodbus_EP2 === true)
					{
						decoded.data.multimodbus_EP2_slaveID = bytes[index2];
						index2 = index2 + 1;
						decoded.data.multimodbus_EP2_fnctID = bytes[index2];
						index2 = index2 + 1;
						decoded.data.multimodbus_EP2_datasize = bytes[index2];
						index2 = index2 + 1;
						decoded.data.multimodbus_EP2_payload = ""
						if (bytes[index2] === undefined ) return decoded;
						for( var j = 0; j < decoded.data.multimodbus_EP2_datasize; j++ )
						{
							temp_hex_str   = bytes[index2+j].toString( 16 ).toUpperCase( );
							if( temp_hex_str.length == 1 )
							{
								temp_hex_str = "0" + temp_hex_str;
							}
							decoded.data.multimodbus_EP2_payload += temp_hex_str;
						}
						index2 = index2 + decoded.data.multimodbus_EP2_datasize;
					}
					if (decoded.data.multimodbus_EP3 === true)
					{
						decoded.data.multimodbus_EP3_slaveID = bytes[index2];
						index2 = index2 + 1;
						decoded.data.multimodbus_EP3_fnctID = bytes[index2];
						index2 = index2 + 1;
						decoded.data.multimodbus_EP3_datasize = bytes[index2];
						index2 = index2 + 1;
						decoded.data.multimodbus_EP3_payload = ""
						if (bytes[index2] === undefined ) return decoded;
						for( var j = 0; j < decoded.data.multimodbus_EP3_datasize; j++ )
						{
							temp_hex_str   = bytes[index2+j].toString( 16 ).toUpperCase( );
							if( temp_hex_str.length == 1 )
							{
								temp_hex_str = "0" + temp_hex_str;
							}
							decoded.data.multimodbus_EP3_payload += temp_hex_str;
						}
						index2 = index2 + decoded.data.multimodbus_EP3_datasize;
					}
					if (decoded.data.multimodbus_EP4 === true)
					{
						decoded.data.multimodbus_EP4_slaveID = bytes[index2];
						index2 = index2 + 1;
						decoded.data.multimodbus_EP4_fnctID = bytes[index2];
						index2 = index2 + 1;
						decoded.data.multimodbus_EP4_datasize = bytes[index2];
						index2 = index2 + 1;
						decoded.data.multimodbus_EP4_payload = ""
						if (bytes[index2] === undefined ) return decoded;
						for( var j = 0; j < decoded.data.multimodbus_EP4_datasize; j++ )
						{
							temp_hex_str   = bytes[index2+j].toString( 16 ).toUpperCase( );
							if( temp_hex_str.length == 1 )
							{
								temp_hex_str = "0" + temp_hex_str;
							}
							decoded.data.multimodbus_EP4_payload += temp_hex_str;
						}
						index2 = index2 + decoded.data.multimodbus_EP4_datasize;
					}
					if (decoded.data.multimodbus_EP5 === true)
					{
						decoded.data.multimodbus_EP5_slaveID = bytes[index2];
						index2 = index2 + 1;
						decoded.data.multimodbus_EP5_fnctID = bytes[index2];
						index2 = index2 + 1;
						decoded.data.multimodbus_EP5_datasize = bytes[index2];
						index2 = index2 + 1;
						decoded.data.multimodbus_EP5_payload = ""
						if (bytes[index2] === undefined ) return decoded;
						for( var j = 0; j < decoded.data.multimodbus_EP5_datasize; j++ )
						{
							temp_hex_str   = bytes[index2+j].toString( 16 ).toUpperCase( );
							if( temp_hex_str.length == 1 )
							{
								temp_hex_str = "0" + temp_hex_str;
							}
							decoded.data.multimodbus_EP5_payload += temp_hex_str;
						}
						index2 = index2 + decoded.data.multimodbus_EP5_datasize;
					}
					if (decoded.data.multimodbus_EP6 === true)
					{
						decoded.data.multimodbus_EP6_slaveID = bytes[index2];
						index2 = index2 + 1;
						decoded.data.multimodbus_EP6_fnctID = bytes[index2];
						index2 = index2 + 1;
						decoded.data.multimodbus_EP6_datasize = bytes[index2];
						index2 = index2 + 1;
						decoded.data.multimodbus_EP6_payload = ""
						if (bytes[index2] === undefined ) return decoded;
						for( var j = 0; j < decoded.data.multimodbus_EP6_datasize; j++ )
						{
							temp_hex_str   = bytes[index2+j].toString( 16 ).toUpperCase( );
							if( temp_hex_str.length == 1 )
							{
								temp_hex_str = "0" + temp_hex_str;
							}
							decoded.data.multimodbus_EP6_payload += temp_hex_str;
						}
						index2 = index2 + decoded.data.multimodbus_EP6_datasize;
					}
					if (decoded.data.multimodbus_EP7 === true)
					{
						decoded.data.multimodbus_EP7_slaveID = bytes[index2];
						index2 = index2 + 1;
						decoded.data.multimodbus_EP7_fnctID = bytes[index2];
						index2 = index2 + 1;
						decoded.data.multimodbus_EP7_datasize = bytes[index2];
						index2 = index2 + 1;
						decoded.data.multimodbus_EP7_payload = ""
						if (bytes[index2] === undefined ) return decoded;
						for( var j = 0; j < decoded.data.multimodbus_EP7_datasize; j++ )
						{
							temp_hex_str   = bytes[index2+j].toString( 16 ).toUpperCase( );
							if( temp_hex_str.length == 1 )
							{
								temp_hex_str = "0" + temp_hex_str;
							}
							decoded.data.multimodbus_EP7_payload += temp_hex_str;
						}
						index2 = index2 + decoded.data.multimodbus_EP7_datasize;
					}
					if (decoded.data.multimodbus_EP8 === true)
					{
						decoded.data.multimodbus_EP8_slaveID = bytes[index2];
						index2 = index2 + 1;
						decoded.data.multimodbus_EP8_fnctID = bytes[index2];
						index2 = index2 + 1;
						decoded.data.multimodbus_EP8_datasize = bytes[index2];
						index2 = index2 + 1;
						decoded.data.multimodbus_EP8_payload = ""
						if (bytes[index2] === undefined ) return decoded;
						for( var j = 0; j < decoded.data.multimodbus_EP8_datasize; j++ )
						{
							temp_hex_str   = bytes[index2+j].toString( 16 ).toUpperCase( );
							if( temp_hex_str.length == 1 )
							{
								temp_hex_str = "0" + temp_hex_str;
							}
							decoded.data.multimodbus_EP8_payload += temp_hex_str;
						}
						index2 = index2 + decoded.data.multimodbus_EP8_datasize;
					}
					if (decoded.data.multimodbus_EP9 === true)
					{
						decoded.data.multimodbus_EP6_slaveID = bytes[index2];
						index2 = index2 + 1;
						decoded.data.multimodbus_EP6_fnctID = bytes[index2];
						index2 = index2 + 1;
						decoded.data.multimodbus_EP6_datasize = bytes[index2];
						index2 = index2 + 1;
						decoded.data.multimodbus_EP6_payload = ""
						if (bytes[index2] === undefined ) return decoded;
						for( var j = 0; j < decoded.data.multimodbus_EP6_datasize; j++ )
						{
							temp_hex_str   = bytes[index2+j].toString( 16 ).toUpperCase( );
							if( temp_hex_str.length == 1 )
							{
								temp_hex_str = "0" + temp_hex_str;
							}
							decoded.data.multimodbus_EP6_payload += temp_hex_str;
						}
						index2 = index2 + decoded.data.multimodbus_EP6_datasize;
					}

				}
				
				//simple metering
				if (  (clusterdID === 0x0052 ) & (attributID === 0x0000)) {
					decoded.data.active_energy_Wh = UintToInt(bytes[index+1]*256*256+bytes[index+2]*256+bytes[index+3],3);
					decoded.data.reactive_energy_Varh = UintToInt(bytes[index+4]*256*256+bytes[index+5]*256+bytes[index+6],3);
					decoded.data.nb_samples = (bytes[index+7]*256+bytes[index+8]);
					decoded.data.active_power_W = UintToInt(bytes[index+9]*256+bytes[index+10],2);
					decoded.data.reactive_power_VAR = UintToInt(bytes[index+11]*256+bytes[index+12],2);
				}
				// lorawan message type
				if (  (clusterdID === 0x8004 ) & (attributID === 0x0000)) {
				  if (bytes[index] === 1)
					decoded.data.message_type = "confirmed";
				  if (bytes[index] === 0)
					decoded.data.message_type = "unconfirmed";
				}
				
				// lorawan retry
				if (  (clusterdID === 0x8004 ) & (attributID === 0x0001)) {
						decoded.data.nb_retry= bytes[index] ;
				}
				
				// lorawan reassociation
				if (  (clusterdID === 0x8004 ) & (attributID === 0x0002)) {
					decoded.data.period_in_minutes = bytes[index+1] *256+bytes[index+2];
					decoded.data.nb_err_frames = bytes[index+3] *256+bytes[index+4];
				}
				// configuration node power desc
				if (   (clusterdID === 0x0050 ) & (attributID === 0x0006)) {
				  index2 = index + 3;
				  if ((bytes[index+2] &0x01) === 0x01) {decoded.data.main_or_external_voltage = (bytes[index2]*256+bytes[index2+1])/1000;index2=index2+2;}
				  if ((bytes[index+2] &0x02) === 0x02) {decoded.data.rechargeable_battery_voltage = (bytes[index2]*256+bytes[index2+1])/1000;index2=index2+2;}
				  if ((bytes[index+2] &0x04) === 0x04) {decoded.data.disposable_battery_voltage = (bytes[index2]*256+bytes[index2+1])/1000;index2=index2+2;}
				  if ((bytes[index+2] &0x08) === 0x08) {decoded.data.solar_harvesting_voltage = (bytes[index2]*256+bytes[index2+1])/1000;index2=index2+2;}
				  if ((bytes[index+2] &0x10) === 0x10) {decoded.data.tic_harvesting_voltage = (bytes[index2]*256+bytes[index2+1])/1000;index2=index2+2;}
				}
				//energy and power metering
				if (  (clusterdID === 0x800a) & (attributID === 0x0000)) {
					index2 = index;
					decoded.data.sum_positive_active_energy_Wh = UintToInt(bytes[index2+1]*256*256*256+bytes[index2+2]*256*256+bytes[index2+3]*256+bytes[index2+4],4);
					index2 = index2 + 4; 
					decoded.data.sum_negative_active_energy_Wh = UintToInt(bytes[index2+1]*256*256*256+bytes[index2+2]*256*256+bytes[index2+3]*256+bytes[index2+4],4);
					index2 = index2 + 4; 
					decoded.data.sum_positive_reactive_energy_Wh = UintToInt(bytes[index2+1]*256*256*256+bytes[index2+2]*256*256+bytes[index2+3]*256+bytes[index2+4],4);
					index2 = index2 + 4; 
					decoded.data.sum_negative_reactive_energy_Wh = UintToInt(bytes[index2+1]*256*256*256+bytes[index2+2]*256*256+bytes[index2+3]*256+bytes[index2+4],4);
					index2 = index2 + 4; 
					decoded.data.positive_active_power_W = UintToInt(bytes[index2+1]*256*256*256+bytes[index2+2]*256*256+bytes[index2+3]*256+bytes[index2+4],4);
					index2 = index2 + 4; 
					decoded.data.negative_active_power_W = UintToInt(bytes[index2+1]*256*256*256+bytes[index2+2]*256*256+bytes[index2+3]*256+bytes[index2+4],4);
					index2 = index2 + 4; 
					decoded.data.positive_reactive_power_W = UintToInt(bytes[index2+1]*256*256*256+bytes[index2+2]*256*256+bytes[index2+3]*256+bytes[index2+4],4);
					index2 = index2 + 4; 
					decoded.data.negative_reactive_power_W = UintToInt(bytes[index2+1]*256*256*256+bytes[index2+2]*256*256+bytes[index2+3]*256+bytes[index2+4],4);
				}
				//energy and power metering
				if (  (clusterdID === 0x800b) & (attributID === 0x0000)) {
					index2 = index;
					decoded.data.Vrms = UintToInt(bytes[index2+1]*256+bytes[index2+2],2)/10;
					index2 = index2 + 2; 
					decoded.data.Irms = UintToInt(bytes[index2+1]*256+bytes[index2+2],2)/10;
					index2 = index2 + 2; 
					decoded.data.phase_angle = UintToInt(bytes[index2+1]*256+bytes[index2+2],2);
				}
			}
		
			//decode configuration response
			if(cmdID === 0x07){
				//AttributID
				attributID = bytes[6]*256 + bytes[7];decoded.zclheader.attributID = decimalToHex(attributID,4);
				//status
				decoded.zclheader.status = bytes[4];
				//batch
				decoded.zclheader.batch = bytes[5];
			}
			
			
			//decode read configuration response
			if(cmdID === 0x09){
				//AttributID
				attributID = bytes[6]*256 + bytes[7];decoded.zclheader.attributID = decimalToHex(attributID,4);
				//status
				decoded.zclheader.status = bytes[4];
				//batch
				decoded.zclheader.batch = bytes[5];
				
				//AttributType
				decoded.zclheader.attribut_type = bytes[8];
				//min
				decoded.zclheader.min = {}
				if ((bytes[9] & 0x80) === 0x80) {decoded.zclheader.min.value = (bytes[9]-0x80)*256+bytes[10];decoded.zclheader.min.unity = "minutes";} else {decoded.zclheader.min.value = bytes[9]*256+bytes[10];decoded.zclheader.min.unity = "seconds";}
				//max
				decoded.zclheader.max = {}
				if ((bytes[9] & 0x80) === 0x80) {decoded.zclheader.max.value = (bytes[9]-0x80)*256+bytes[10];decoded.zclheader.max.unity = "minutes";} else {decoded.zclheader.max.value = bytes[9]*256+bytes[10];decoded.zclheader.max.unity = "seconds";}
				decoded.lora.payload  = "";

			}
		
		}
		else
		{
			decoded.batch = {};
			decoded.batch.report = "batch";
		}
	}

  return decoded;
}

```

3. Copy/Paste the following test payload into the field `Payload` and press `Test`

> Attention: all incoming and outgoing frames have to be sent on port 125

```
110A000F04022300000DF5
```

4. You should see the following result

```json
{
  "data": {
    "counter": 3573
  },
  "lora": {
    "date": "2020-07-15T01:50:23.645Z",
    "payload": "110A000F04022300000DF5",
    "port": 125
  },
  "zclheader": {
    "attributID": "0x0402",
    "clusterdID": "0x000F",
    "cmdID": "0x0A",
    "endpoint": 0,
    "report": "standard"
  }
}
```

> <b>counter</b> -> Counter [impulses]<br>
> Attention, the amount of energy or water flow per impulse is meter specific. Electricity meters have e.g. 1'000 impulses per kWh.

5. Press `save payload functions`

> Now you should be able to see the decoded data of your sensor in the tab `Data`.<br>
> Trigger a new telegram by pressing the small button.<br><br>

### 6. Change measurement interval
> The Flash'O sends per default a telegram once a day with the counter value.<br>
> If you want to change that you have to send a telegram to the device. To do so follow the instructions below.<br>
> There are two properties of the Flash'O, the min sending interval in seconds and the impuls count.<br>

1. [Log in](https://console.thethingsnetwork.org/applications) and open the `application`
2. Select the device in the tab `Devices` and scroll down to `Downlink`
3. Copy/paste one of the following payloads into the big field, change the `FPort to 125` and press `Send`
   - every 15 minutes and every 200 impulses: `11 06 00 0f 00 04 02 23 00 00 03 84 00 00 00 c8`
   - every 1 day: `11 06 00 0f 00 04 02 23 00 00 00 00 00 00 00 00`

> For details and other combinations see [Frame Examples](http://support.nke-watteco.com/flasho/#FrameExamples)

### 7. Add Storage
> Normally incoming data in the network only gets forwarded to the end user applications and does not get saved.<br>
> "The Things Network" offers a seven day storage which is accessible through an API. To activate it, follow these steps.

1. [Log in](https://console.thethingsnetwork.org/applications) and open the `application`
2. Select the tab `Integrations > (+) add integration`
3. Select `Data Storage` and press `Add integration`

> Now we're done with the configuration :-)