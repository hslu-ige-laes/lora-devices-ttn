---
layout: default
title: Decentlab :: DL-ATM22
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
var decentlab_decoder = {
  PROTOCOL_VERSION: 2,
  SENSORS: [
    {length: 8,
     values: [{name: 'wind_speed',
               displayName: 'Wind speed',
               convert: function (x) { return (x[0] - 32768) / 100; },
               unit: 'm⋅s⁻¹'},
              {name: 'wind_direction',
               displayName: 'Wind direction',
               convert: function (x) { return (x[1] - 32768) / 10; },
               unit: '°'},
              {name: 'maximum_wind_speed',
               displayName: 'Maximum wind speed',
               convert: function (x) { return (x[2] - 32768) / 100; },
               unit: 'm⋅s⁻¹'},
              {name: 'air_temperature',
               displayName: 'Air temperature',
               convert: function (x) { return (x[3] - 32768) / 10; },
               unit: '°C'},
              {name: 'x_orientation_angle',
               displayName: 'X orientation angle',
               convert: function (x) { return (x[4] - 32768) / 10; },
               unit: '°'},
              {name: 'y_orientation_angle',
               displayName: 'Y orientation angle',
               convert: function (x) { return (x[5] - 32768) / 10; },
               unit: '°'},
              {name: 'north_wind_speed',
               displayName: 'North wind speed',
               convert: function (x) { return (x[6] - 32768) / 100; },
               unit: 'm⋅s⁻¹'},
              {name: 'east_wind_speed',
               displayName: 'East wind speed',
               convert: function (x) { return (x[7] - 32768) / 100; },
               unit: 'm⋅s⁻¹'}]},
    {length: 1,
     values: [{name: 'battery_voltage',
               displayName: 'Battery voltage',
               convert: function (x) { return x[0] / 1000; },
               unit: 'V'}]}
  ],

  read_int: function (bytes, pos) {
    return (bytes[pos] << 8) + bytes[pos + 1];
  },

  decode: function (msg) {
    var bytes = msg;
    var i, j;
    if (typeof msg === 'string') {
      bytes = [];
      for (i = 0; i < msg.length; i += 2) {
        bytes.push(parseInt(msg.substring(i, i + 2), 16));
      }
    }

    var version = bytes[0];
    if (version != this.PROTOCOL_VERSION) {
      return {error: "protocol version " + version + " doesn't match v2"};
    }

    var deviceId = this.read_int(bytes, 1);
    var flags = this.read_int(bytes, 3);
    var result = {'protocol_version': version, 'device_id': deviceId};
    // decode payload
    var pos = 5;
    for (i = 0; i < this.SENSORS.length; i++, flags >>= 1) {
      if ((flags & 1) !== 1)
        continue;

      var sensor = this.SENSORS[i];
      var x = [];
      // convert data to 16-bit integer array
      for (j = 0; j < sensor.length; j++) {
        x.push(this.read_int(bytes, pos));
        pos += 2;
      }

      // decode sensor values
      for (j = 0; j < sensor.values.length; j++) {
        var value = sensor.values[j];
        if ('convert' in value) {
          result[value.name] = {displayName: value.displayName,
                                value: value.convert.bind(this)(x)};
          if ('unit' in value)
            result[value.name]['unit'] = value.unit;
        }
      }
    }
    return result;
  }
};

function decodeUplink(input) {
  return {
    data: decentlab_decoder.decode(input.bytes),
    warnings: [],
    errors: []
  };
}
```


