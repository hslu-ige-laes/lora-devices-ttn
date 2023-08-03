---
layout: default
title: Decentlab - dl-atm22
parent: Sensors
---

<img src="https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/decentlab-dl-atm22_02.jpg" width="250" align="right" class="inline"/>

# Decentlab - DL-ATM22
{: .no_toc }

- Manufacturer: <a href="https://www.decentlab.com/" target="_blank">Decentlab</a>
- Product: <a href="https://www.decentlab.com/products/wind-speed-wind-direction-and-temperature-sensor-for-lorawan" target="_blank">DL-ATM22</a>

Wind speed, wind direction and temperature sensor.

---
## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Specifications
- Outdoor device
- Price ca. CHF 1'546.- (Sept. 2019)
- Sensors
  - <b>Horizontal Wind Speed</b><br>
	  Range: 0 … 30 [m/s], Resolution 0.01 m/s, Accuracy max(0.3 m/s;  3% of measurement)
  - <b>Wind Gust</b><br>
	  Range: 0 … 30 m/s, Resolution 0.01 [m/s], Accuracy max(0.3 m/s; 3% of measurement)
  - <b>Wind Direction</b><br>
	  Range: 0 … 359 [°], Resolution: 1°, Accuracy: ±5°
  - <b>Tilt</b><br>
	  Range: -90° … +90 [°], Resolution: 0.1°, Accuracy: ±1°
  - <b>Temperature:</b><br>
	  Range: -50 ... +60 [°C], Resolution: 0.1 °C, Accuracy: ±0.6 °C
		not protected from solar radiation
- Power Supply: 2 C alkaline batteries
- Size:
  Device 135 × 81 × 70 mm
	Sensor: 100 x 100 x 160 mm
- Weight: 1020 g including batteries and sensor (890 g without batteries)
- Operating conditions: -20 ... 50 °C, 0 ... 100 % RH
- Cable length: 5 m

---

## Documents
  - [Payload decoder](https://github.com/decentlab/decentlab-decoders/blob/master/DL-ATM22/DL-ATM22.js)
  - [Datasheet (2023-08-03)](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/decentlab-dl-atm22_01.pdf)
  
---

## Ordering Info

[Ordering Link](https://www.decentlab.com/quote-request)

---

## Device specific Information
### Operating Modes
**The device has four operating modes**
- **Reset**<br>
  System (re-)start; both LEDs fade in and out.
- **Active mode (ON)**<br>
  Periodic measurements and data transmissions; green LED flashes for each measurement.
- **Sleep mode (OFF)**<br>
  No measurements and data transmissions (power save mode, for shelf storage). LEDs are off.
- **Test mode**<br>
  Measurements and data transmissions at fastest possible rates; blue LED is on.

**Switching between operating modes**
- The user button allows to switch between the operating modes as shown in the two llustrations below.
- To perform a device reset, switch to sleep mode first (if necessary) by pushing and holding the button for 3 seconds until the LEDs flash three times; wait 3 seconds; then push and hold the button for 3 seconds until the LEDs fade in and out.
- To switch between active and test mode, push the button for 1 second (blue LED on / off). If the blue LED is off, the device is in active or sleep mode. If the blue LED is on, the device is in test mode.

![Switching between active and sleep mode (switch off / on, reset)](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/decentlab-dl-atm22_03.png "Switching between active and sleep mode (switch off / on, reset)")

![Switching between active and test mode](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/decentlab-dl-atm22_04.png "Switching between active and test mode")

**HINT**<br>
To check whether the device is active or in sleep mode (on or off), push the button twice
- if the blue LED goes on and off, the device is in active mode
- otherwise, the device is in sleep mode

---

## Adding the Device to TTN
- Before a device can communicate via "The Things Network" we need to register it with an application.<br>

1. [Create a new application](https://hslu-ige-laes.github.io/lora-devices-ttn/docs/getting_started#create-a-new-application)
2. Under `Overview` click `(+) Register device`
3. Under `Input method` select `Select the end device in the LoRaWAN Device Repository`
4. Enter the following device information
   - `End device brand` select `Decentlab GmbH`
   - `Model` select `DL-ATM22`
   - `Hardware Ver.` select `1`
   - `Firmware` select `1.5.7`
	 - `Profile (Region)` select `EU_863_870`
5. Under `Frequency plan` select `Europe 863-870 Mhz (SF9 for RX2 - recommended)`
6. Under `JoinEUI` enter the `App EUI` from the sticker
7. Enter as well the `DevEUI` and the `AppKey` from the sticker
8. Set an end-device name
9. Press `Register end device`
10. Switch on the device (see section Operating Modes from above)

- Now the device should join the network and you can see the incoming telegrams in the `Live data` section
- The payload formatter should already be preset. If not, you can copy/paste it from below

---

## Payload formatter

```javascript
var decentlab_codec = {
  DOWNLINK_COMMANDS: {
    '0001': {
      desc: 'Set sampling period in seconds (1...65535).',
      name: 'set period',
      min_firmware: '0.2.7',
    },
    '0002': {
      desc: 'Set sampling period in seconds (1...65535) + save settings.',
      name: 'set period + save',
      min_firmware: '0.2.7',
    },
    '0003': {
      desc: 'Set default Tx data rate. Used for next Tx. Actual data rate for following Tx may vary, if ADR is enabled.',
      name: 'set dr',
      min_firmware: '0.2.7',
    },
    '0004': {
      desc: 'Set default Tx data rate. Used for next Tx. Actual data rate for following Tx may vary, if ADR is enabled + save settings.',
      name: 'set dr + save',
      min_firmware: '0.2.7',
    },
    '0005': {
      desc: 'Enable ADR.',
      name: 'set adr on',
      min_firmware: '0.2.7',
      noparam: true,
    },
    '0006': {
      desc: 'Enable ADR + save settings.',
      name: 'set adr on + save',
      min_firmware: '0.2.7',
      noparam: true,
    },
    '0007': {
      desc: 'Disable ADR.',
      name: 'set adr off',
      min_firmware: '0.2.7',
      noparam: true,
    },
    '0008': {
      desc: 'Disable ADR + save settings.',
      name: 'set adr off + save',
      min_firmware: '0.2.7',
      noparam: true,
    },
    '0009': {
      desc: 'Set minimum data rate (overrides ADR settings).',
      name: 'set dr_min',
      min_firmware: '1.2.0',
    },
    '000A': {
      desc: 'Set minimum data rate (overrides ADR settings) + save settings.',
      name: 'set dr_min + save',
      min_firmware: '1.2.0',
    },
    '000B': {
      desc: 'Set maximum data rate (overrides ADR settings).',
      name: 'set dr_max',
      min_firmware: '1.2.0',
    },
    '000C': {
      desc: 'Set maximum data rate (overrides ADR settings) + save settings.',
      name: 'set dr_max + save',
      min_firmware: '1.2.0',
    },
    '000D': {
      desc: 'Set minimum Tx power index (overrides ADR settings).',
      name: 'set pwridx_min',
      min_firmware: '1.2.0',
    },
    '000E': {
      desc: 'Set minimum Tx power index (overrides ADR settings) + save settings.',
      name: 'set pwridx_min + save',
      min_firmware: '1.2.0',
    },
    '000F': {
      desc: 'Set maximum Tx power index (overrides ADR settings).',
      name: 'set pwridx_max',
      min_firmware: '1.2.0',
    },
    '0010': {
      desc: 'Set maximum Tx power index (overrides ADR settings) + save settings.',
      name: 'set pwridx_max + save',
      min_firmware: '1.2.0',
    },
    '0011': {
      desc: 'Set send period. Examples: 0 or 1: send after every sampling; 4: send after every fourth sampling.',
      name: 'set send_period',
      min_firmware: '1.4.0',
    },
    '0012': {
      desc: 'Set send period. Examples: 0 or 1: send after every sampling; 4: send after every fourth sampling + save settings.',
      name: 'set send_period + save',
      min_firmware: '1.4.0',
    },
    '0013': {
      desc: 'Set re-join period in hours (0...1000). Examples: 24: re-join network every 24 hours. 0: never re- join.',
      name: 'set join_period',
      min_firmware: '1.4.2',
    },
    '0014': {
      desc: 'Set re-join period in hours (0...1000). Examples: 24: re-join network every 24 hours. 0: never re- join + save settings.',
      name: 'set join_period + save',
      min_firmware: '1.4.2',
    },
    '0015': {
      desc: 'Set default Tx power index. Used for next Tx. Actual pwridx for following Tx may vary, if ADR is enabled.',
      name: 'set pwridx',
      min_firmware: '1.4.5',
    },
    '0016': {
      desc: 'Set default Tx power index. Used for next Tx. Actual pwridx for following Tx may vary, if ADR is enabled + save settings.',
      name: 'set pwridx + save',
      min_firmware: '1.4.5',
    },
    '0017': {
      desc: 'Set link check period (default: 36).',
      name: 'set linkcheck_period',
      min_firmware: '1.5.0',
    },
    '0018': {
      desc: 'Set link check period (default: 36) + save settings.',
      name: 'set linkcheck_period + save',
      min_firmware: '1.5.0',
    },
    '0019': {
      desc: 'Set link check tolerance (default: 6).',
      name: 'set linkcheck_tolerance',
      min_firmware: '1.5.0',
    },
    '001A': {
      desc: 'Set link check tolerance (default: 6) + save settings.',
      name: 'set linkcheck_tolerance + save',
      min_firmware: '1.5.0',
    },
    '001B': {
      desc: 'Set link check limit (default: 12).',
      name: 'set linkcheck_limit',
      min_firmware: '1.5.0',
    },
    '001C': {
      desc: 'Set link check limit (default: 12) + save settings.',
      name: 'set linkcheck_limit + save',
      min_firmware: '1.5.0',
    },
    '001D': {
      desc: 'Set LoRaWAN uplink port (1...223, default: 1).',
      name: 'set port',
      min_firmware: '1.6.0',
    },
    '001E': {
      desc: 'Set LoRaWAN uplink port (1...223, default: 1)  + save settings.',
      name: 'set port + save',
      min_firmware: '1.6.0',
    },
    '0020': {
      desc: 'Set parameter 0 (0...65534; 65535: invalid).',
      name: 'set param 0',
      min_firmware: '1.4.0',
    },
    '0021': {
      desc: 'Set parameter 1 (0...65534; 65535: invalid).',
      name: 'set param 1',
      min_firmware: '1.4.0',
    },
    '0022': {
      desc: 'Set parameter 2 (0...65534; 65535: invalid).',
      name: 'set param 2',
      min_firmware: '1.4.0',
    },
    '0023': {
      desc: 'Set parameter 3 (0...65534; 65535: invalid).',
      name: 'set param 3',
      min_firmware: '1.4.0',
    },
    '0024': {
      desc: 'Set parameter 4 (0...65534; 65535: invalid).',
      name: 'set param 4',
      min_firmware: '1.4.0',
    },
    '0025': {
      desc: 'Set parameter 5 (0...65534; 65535: invalid).',
      name: 'set param 5',
      min_firmware: '1.4.0',
    },
    '0026': {
      desc: 'Set parameter 6 (0...65534; 65535: invalid).',
      name: 'set param 6',
      min_firmware: '1.4.0',
    },
    '0027': {
      desc: 'Set parameter 7 (0...65534; 65535: invalid).',
      name: 'set param 7',
      min_firmware: '1.4.0',
    },
    '0028': {
      desc: 'Set parameter 8 (0...65534; 65535: invalid).',
      name: 'set param 8',
      min_firmware: '1.4.0',
    },
    '0029': {
      desc: 'Set parameter 9 (0...65534; 65535: invalid).',
      name: 'set param 9',
      min_firmware: '1.4.0',
    },
    '002A': {
      desc: 'Set parameter 10 (0...65534; 65535: invalid).',
      name: 'set param 10',
      min_firmware: '1.4.0',
    },
    '002B': {
      desc: 'Set parameter 11 (0...65534; 65535: invalid).',
      name: 'set param 11',
      min_firmware: '1.4.0',
    },
    '002C': {
      desc: 'Set parameter 12 (0...65534; 65535: invalid).',
      name: 'set param 12',
      min_firmware: '1.4.0',
    },
    '002D': {
      desc: 'Set parameter 13 (0...65534; 65535: invalid).',
      name: 'set param 13',
      min_firmware: '1.4.0',
    },
    '002E': {
      desc: 'Set parameter 14 (0...65534; 65535: invalid).',
      name: 'set param 14',
      min_firmware: '1.4.0',
    },
    '002F': {
      desc: 'Set parameter 15 (0...65534; 65535: invalid). ',
      name: 'set param 15',
      min_firmware: '1.4.0',
    },
    '0030': {
      desc: 'Set parameter 0 (0...65534; 65535: invalid) + save settings.',
      name: 'set param 0 + save',
      min_firmware: '1.4.0',
    },
    '0031': {
      desc: 'Set parameter 1 (0...65534; 65535: invalid) + save settings.',
      name: 'set param 1 + save',
      min_firmware: '1.4.0',
    },
    '0032': {
      desc: 'Set parameter 2 (0...65534; 65535: invalid) + save settings.',
      name: 'set param 2 + save',
      min_firmware: '1.4.0',
    },
    '0033': {
      desc: 'Set parameter 3 (0...65534; 65535: invalid) + save settings.',
      name: 'set param 3 + save',
      min_firmware: '1.4.0',
    },
    '0034': {
      desc: 'Set parameter 4 (0...65534; 65535: invalid) + save settings.',
      name: 'set param 4 + save',
      min_firmware: '1.4.0',
    },
    '0035': {
      desc: 'Set parameter 5 (0...65534; 65535: invalid) + save settings.',
      name: 'set param 5 + save',
      min_firmware: '1.4.0',
    },
    '0036': {
      desc: 'Set parameter 6 (0...65534; 65535: invalid) + save settings.',
      name: 'set param 6 + save',
      min_firmware: '1.4.0',
    },
    '0037': {
      desc: 'Set parameter 7 (0...65534; 65535: invalid) + save settings.',
      name: 'set param 7 + save',
      min_firmware: '1.4.0',
    },
    '0038': {
      desc: 'Set parameter 8 (0...65534; 65535: invalid) + save settings.',
      name: 'set param 8 + save',
      min_firmware: '1.4.0',
    },
    '0039': {
      desc: 'Set parameter 9 (0...65534; 65535: invalid) + save settings.',
      name: 'set param 9 + save',
      min_firmware: '1.4.0',
    },
    '003A': {
      desc: 'Set parameter 10 (0...65534; 65535: invalid) + save settings.',
      name: 'set param 10 + save',
      min_firmware: '1.4.0',
    },
    '003B': {
      desc: 'Set parameter 11 (0...65534; 65535: invalid) + save settings.',
      name: 'set param 11 + save',
      min_firmware: '1.4.0',
    },
    '003C': {
      desc: 'Set parameter 12 (0...65534; 65535: invalid) + save settings.',
      name: 'set param 12 + save',
      min_firmware: '1.4.0',
    },
    '003D': {
      desc: 'Set parameter 13 (0...65534; 65535: invalid) + save settings.',
      name: 'set param 13 + save',
      min_firmware: '1.4.0',
    },
    '003E': {
      desc: 'Set parameter 14 (0...65534; 65535: invalid) + save settings.',
      name: 'set param 14 + save',
      min_firmware: '1.4.0',
    },
    '003F': {
      desc: 'Set parameter 15 (0...65534; 65535: invalid) + save settings.',
      name: 'set param 15 + save',
      min_firmware: '1.4.0',
    },
    '0050': {
      desc: 'Set (sub-)sampling period of sensor 0.',
      name: 'set sensor_period 0',
      advanced: true,
      min_firmware: '1.4.5',
    },
    '0051': {
      desc: 'Set (sub-)sampling period of sensor 1.',
      name: 'set sensor_period 1',
      advanced: true,
      min_firmware: '1.4.5',
    },
    '0052': {
      desc: 'Set (sub-)sampling period of sensor 2.',
      name: 'set sensor_period 2',
      advanced: true,
      min_firmware: '1.4.5',
    },
    '0053': {
      desc: 'Set (sub-)sampling period of sensor 3.',
      name: 'set sensor_period 3',
      advanced: true,
      min_firmware: '1.4.5',
    },
    '0054': {
      desc: 'Set (sub-)sampling period of sensor 4.',
      name: 'set sensor_period 4',
      advanced: true,
      min_firmware: '1.4.5',
    },
    '0055': {
      desc: 'Set (sub-)sampling period of sensor 5.',
      name: 'set sensor_period 5',
      advanced: true,
      min_firmware: '1.4.5',
    },
    '0056': {
      desc: 'Set (sub-)sampling period of sensor 6.',
      name: 'set sensor_period 6',
      advanced: true,
      min_firmware: '1.4.5',
    },
    '0057': {
      desc: 'Set (sub-)sampling period of sensor 7.',
      name: 'set sensor_period 7',
      advanced: true,
      min_firmware: '1.4.5',
    },
    '0058': {
      desc: 'Set (sub-)sampling period of sensor 8.',
      name: 'set sensor_period 8',
      advanced: true,
      min_firmware: '1.4.5',
    },
    '0059': {
      desc: 'Set (sub-)sampling period of sensor 9.',
      name: 'set sensor_period 9',
      advanced: true,
      min_firmware: '1.4.5',
    },
    '0060': {
      desc: 'Set (sub-)sampling period of sensor 0 + save settings.',
      name: 'set sensor_period 0 + save',
      advanced: true,
      min_firmware: '1.4.5',
    },
    '0061': {
      desc: 'Set (sub-)sampling period of sensor 1 + save settings.',
      name: 'set sensor_period 1 + save',
      advanced: true,
      min_firmware: '1.4.5',
    },
    '0062': {
      desc: 'Set (sub-)sampling period of sensor 2 + save settings.',
      name: 'set sensor_period 2 + save',
      advanced: true,
      min_firmware: '1.4.5',
    },
    '0063': {
      desc: 'Set (sub-)sampling period of sensor 3 + save settings.',
      name: 'set sensor_period 3 + save',
      advanced: true,
      min_firmware: '1.4.5',
    },
    '0064': {
      desc: 'Set (sub-)sampling period of sensor 4 + save settings.',
      name: 'set sensor_period 4 + save',
      advanced: true,
      min_firmware: '1.4.5',
    },
    '0065': {
      desc: 'Set (sub-)sampling period of sensor 5 + save settings.',
      name: 'set sensor_period 5 + save',
      advanced: true,
      min_firmware: '1.4.5',
    },
    '0066': {
      desc: 'Set (sub-)sampling period of sensor 6 + save settings.',
      name: 'set sensor_period 6 + save',
      advanced: true,
      min_firmware: '1.4.5',
    },
    '0067': {
      desc: 'Set (sub-)sampling period of sensor 7 + save settings.',
      name: 'set sensor_period 7 + save',
      advanced: true,
      min_firmware: '1.4.5',
    },
    '0068': {
      desc: 'Set (sub-)sampling period of sensor 8 + save settings.',
      name: 'set sensor_period 8 + save',
      advanced: true,
      min_firmware: '1.4.5',
    },
    '0069': {
      desc: 'Set (sub-)sampling period of sensor 9 + save settings.',
      name: 'set sensor_period 9 + save',
      advanced: true,
      min_firmware: '1.4.5',
    },
    FEFE: {
      desc: 'Reset device. Unsaved parameter changes are lost.',
      name: 'reset',
      min_firmware: '0.2.7',
      noparam: true,
    },
    FEF0: {
      desc: 'Erase settings in flash and reset.',
      name: 'factory reset',
      min_firmware: '0.2.7',
      noparam: true,
    },
    FEF1: {
      desc: 'Enter sleep mode (power off).',
      name: 'sleep',
      min_firmware: '1.5.0',
      noparam: true,
    },
  },
  crc16: function (buffer) {
    var crc = 0xffff;
    var odd;
    for (var i = 0; i < buffer.length; i += 2) {
      crc = crc ^ parseInt(buffer.substr(i, 2), 16);
      for (var j = 0; j < 8; j++) {
        odd = crc & 0x0001;
        crc = crc >> 1;
        if (odd) {
          crc = crc ^ 0xa001;
        }
      }
    }
    return crc;
  },
  read_int: function (bytes, pos) {
    return (bytes[pos] << 8) + bytes[pos + 1];
  },
  decode_downlink: function (bytes, f_port) {
    if (bytes.length !== 6) {
      return { errors: ['invalid message length'] };
    }

    if (f_port !== 1) {
      return { errors: ['invalid FPort'] };
    }

    var cmd = ('000' + this.read_int(bytes, 0).toString(16).toUpperCase()).slice(-4);
    if (!(cmd in this.DOWNLINK_COMMANDS)) {
      return {
        errors: ['invalid command'],
      };
    }

    var param = ('000' + this.read_int(bytes, 2).toString(16).toUpperCase()).slice(-4);
    var crc = this.read_int(bytes, 4);

    if (crc !== this.crc16(cmd + param)) {
      return {
        errors: ['invalid CRC'],
      };
    }

    var data = { command: this.DOWNLINK_COMMANDS[cmd].name };
    if (this.DOWNLINK_COMMANDS[cmd].noparam !== true) {
      data.parameter = parseInt(param, 16);
    }
    return { data: data };
  },
  encode_downlink: function (command, parameter) {
    var find_command = function (prev, cur) {
      if (this.DOWNLINK_COMMANDS[cur].name === command) {
        return cur;
      }
      return prev;
    };
    var cmd = Object.keys(this.DOWNLINK_COMMANDS).reduce(find_command.bind(this), undefined);
    if (cmd === undefined) {
      return {
        errors: ['invalid command'],
      };
    }

    var param = 0;
    if (this.DOWNLINK_COMMANDS[cmd].noparam !== true) {
      param = parseInt(parameter).toString(16);
      if (param === 'NaN') {
        return {
          errors: ['invalid parameter'],
        };
      }
    }

    var payload = cmd + ('000' + param).slice(-4);
    var crc = this.crc16(payload);
    var msg = (payload + ('000' + crc.toString(16)).slice(-4)).toUpperCase();
    var bytes = [];
    for (var i = 0; i < msg.length; i += 2) {
      bytes.push(parseInt(msg.substring(i, i + 2), 16));
    }
    return {
      fPort: 1,
      bytes: bytes,
    };
  },
};

function encodeDownlink(input) {
  return decentlab_codec.encode_downlink(input.data.command, input.data.parameter);
}

function decodeDownlink(input) {
  return decentlab_codec.decode_downlink(input.bytes, input.fPort);
}

```


