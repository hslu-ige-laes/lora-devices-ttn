---
layout: default
title: Dragino - RS485-LN
parent: Sensors
---

<img src="https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/dragino-rs485-ln_01.jpeg" width="250" align="right">

# Dragino - RS485-LN
{: .no_toc }

- Manufacturer: <a href="https://www.dragino.com/" target="_blank">Dragino</a>
- Product: <a href="https://www.dragino.com/products/lora-lorawan-end-node/item/154-rs485-ln.html" target="_blank">RS485-LN</a>

The RS485-LN is a versatile RS485-to-LoRaWAN bridge designed for industrial and environmental sensor networks. It allows to connect and monitor up to 15 Modbus RS485 sensors over long-range LoRaWAN wireless links. The device can be configured via the Dragino RS485 Configure Tool.
There is as well a function to listen only to a modbus network and send the listened data over LoRaWAN.
---

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Specifications
- Indoor device
- Price ca. CHF 55.- (09.07.2025)
- LoRaWAN Class A & Class C
- Max 15 RS485 slave Modbus queries per uplink
- In Modbus mode...
  - it is only possible to fetch data from a single device.
  - the device can operate as master and slave (listening for incoming telegrams is supported)
- RS485 interface, Modbus RTU supported
- Power Supply: 7~24V DC
- Power Consumption: Idle 32 mA @12V, TX 65 mA @12V
- Dimensions: 135 x 70 x 30 mm
- Weight: 105 g (device only)
- Default Serial: 9600 baud
- Includes antenna and programming cable

---
## Documents & Downloads
- [Official Dragino RS485-LN product page](https://www.dragino.com/products/lora-lorawan-end-node/item/154-rs485-ln.html)
- [User Manual](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/dragino-rs485-ln_02.pdf)
- [RS485 Configure Tool Manual](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/dragino-rs485-ln_04.pdf)
  - [Direct Windows download (v1.3.1)](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/dragino-rs485-ln_05_tool.zip)
  - [AT Command text file](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/dragino-rs485-ln_03_commands.txt)

## Ordering Info
- Part Number: RS485-LN
- [Ordering Link](https://www.lora-shop.ch/rs485-ln-rs485-modbus-to-lorawan-converter)

---

## TTL-USB Serial Programming 
To configure the RS485-LN with AT commands or the RS485 Tool, connect it via the 3.5mm programming jack using the included programming cable and a USB-TTL adapter.  

Wire colors:

| Cable Color | Signal    | Connects to USB-TTL  |
|-------------|-----------|----------------------|
| **Black**   | GND       | GND                  |
| **Red**     | RXD       | TXD (on adapter)     |
| **White**   | TXD       | RXD (on adapter)     |

**Steps:**

1. Download and start the [RS485 Configure Tool](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/dragino-rs485-ln_05_tool.zip).
2. Wire the USB-TTL adapter with the device cables and plug it into the computer.
3. Plug the programming cable into the RS485-LN's 3.5mm "PROG" jack.
4. Power the RS485-LN via the screw terminal (7-24V DC).
5. Start the RS485 Configure Tool and connect to the device
6. Load the [AT Command text file](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/dragino-rs485-ln_03_commands.txt) into the tool and Write the parameters.
7. Add the device to a ttn application, add the payload decoder from below and restart the device.

**Example**:
If you have 5 Modbus devices (addresses 3,4,5,6,7), each with 3 registers (Temp on 0, Humidity on 1, CO2 on 5, all 16-bit signed), use these AT commands for direct serial configuration, or enter them in the RS485 Configure Tool:

| Device | Sensor   | Modbus Addr | Register | AT+COMMAND         | AT+DATACUT    |
|--------|----------|-------------|----------|--------------------|---------------|
| 3      | Temp     | 3           | 0        | AT+COMMAND1=03 03 00 00 00 01,1 | AT+DATACUT1=7,1,4+5 |
| 3      | Humidity | 3           | 1        | AT+COMMAND2=03 03 00 01 00 01,1 | AT+DATACUT2=7,1,4+5 |
| 3      | CO2      | 3           | 5        | AT+COMMAND3=03 03 00 05 00 01,1 | AT+DATACUT3=7,1,4+5 |
| 4      | Temp     | 4           | 0        | AT+COMMAND4=04 03 00 00 00 01,1 | AT+DATACUT4=7,1,4+5 |
| 4      | Humidity | 4           | 1        | AT+COMMAND5=04 03 00 01 00 01,1 | AT+DATACUT5=7,1,4+5 |
| 4      | CO2      | 4           | 5        | AT+COMMAND6=04 03 00 05 00 01,1 | AT+DATACUT6=7,1,4+5 |
| 5      | Temp     | 5           | 0        | AT+COMMAND7=05 03 00 00 00 01,1 | AT+DATACUT7=7,1,4+5 |
| 5      | Humidity | 5           | 1        | AT+COMMAND8=05 03 00 01 00 01,1 | AT+DATACUT8=7,1,4+5 |
| 5      | CO2      | 5           | 5        | AT+COMMAND9=05 03 00 05 00 01,1 | AT+DATACUT9=7,1,4+5 |
| 6      | Temp     | 6           | 0        | AT+COMMAND10=06 03 00 00 00 01,1 | AT+DATACUT10=7,1,4+5 |
| 6      | Humidity | 6           | 1        | AT+COMMAND11=06 03 00 01 00 01,1 | AT+DATACUT11=7,1,4+5 |
| 6      | CO2      | 6           | 5        | AT+COMMAND12=06 03 00 05 00 01,1 | AT+DATACUT12=7,1,4+5 |
| 7      | Temp     | 7           | 0        | AT+COMMAND13=07 03 00 00 00 01,1 | AT+DATACUT13=7,1,4+5 |
| 7      | Humidity | 7           | 1        | AT+COMMAND14=07 03 00 01 00 01,1 | AT+DATACUT14=7,1,4+5 |
| 7      | CO2      | 7           | 5        | AT+COMMAND15=07 03 00 05 00 01,1 | AT+DATACUT15=7,1,4+5 |

**Set the common parameters:**

```plaintext
AT+BAUDR=9600
AT+PARITY=0
AT+STOPBIT=2
AT+TDC=600000          // 10 min (in ms)
AT+DATAUP=0            // Combine all values in one uplink
```

## Payload Decoder

```javascript
function decodeUplink(input) {
  var bytes = input.bytes;
  var port = input.fPort;
  var data = {};
  var warnings = [];
  var errors = [];

  // Frequency/Firmware info (usually on FPort 5)
  if (port === 5) {
    var bands = {
      0x01: "EU868", 0x02: "US915", 0x03: "IN865", 0x04: "AU915",
      0x05: "KZ865", 0x06: "RU864", 0x07: "AS923", 0x08: "AS923_1",
      0x09: "AS923_2", 0x0A: "AS923_3", 0x0F: "AS923_4", 0x0B: "CN470",
      0x0C: "EU433", 0x0D: "KR920", 0x0E: "MA869"
    };
    data.FREQUENCY_BAND = bands[bytes[0]] || "UNKNOWN";
    data.SUB_BAND = (bytes[1] === 0xff) ? "NULL" : bytes[1];
    data.FIRMWARE_VERSION = (bytes[2]&0x0f) + '.' + ((bytes[3]>>4)&0x0f) + '.' + (bytes[3]&0x0f);
    data.TDC_sec = (bytes[4]<<16) | (bytes[5]<<8) | bytes[6];
    return { data, warnings, errors };
  }

  // Sensor data on FPort 2 (PAYVER + device-major sensor order)
  if (port === 2 && bytes.length >= 31) {
    var devs = [3,4,5,6,7];
    var names = ["temperature_degrC_abs", "humidity_perc_abs", "co2_ppm_abs"];
    var scales = [0.1, 0.1, 1];

    for (var d=0; d<devs.length; d++) { // device
      for (var s=0; s<3; s++) { // sensor
        var idx = d*3 + s;
        var off = 1 + idx*2;
        var label = names[s] + "@device" + devs[d];
        if (off+1 < bytes.length) {
          var raw = (bytes[off]<<8) | bytes[off+1];
          if(raw & 0x8000) raw -= 0x10000;
          var val = raw * scales[s];
          if(s < 2) val = Math.round(val*10)/10;
          data[label] = val;
        } else {
          data[label] = 0;
        }
      }
    }
    return { data, warnings, errors };
  }

  // EXTI/Payver short message (single byte)
  if (port === 2 && bytes.length === 1) {
    data.Node_type = "RS485-LN";
    data.EXTI_Trigger = (bytes[0] & 0x80) ? "TRUE" : "FALSE";
    data.Payver = bytes[0] & 0x7F;
    return { data, warnings, errors };
  }

  errors.push("Unexpected payload: port=" + port + ", length=" + bytes.length);
  return { data: {}, warnings, errors };
}

```
