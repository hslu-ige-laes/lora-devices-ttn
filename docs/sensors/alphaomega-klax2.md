---
layout: default
title: Alpha Omega Technology - Klax 2.0
parent: Sensors
---

<img src="https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/alphaomega-klax2_01.png" width="250" align="right">

# Alpha Omega Technology - Klax 2.0
{: .no_toc }

- Manufacturer: <a href="https://alpha-omega-technology.de/" target="_blank">Seedstudio</a>
- Product: <a href="https://iot-shop.de/en/shop/klax-2-0-lorawan-sml-opto-head-for-modern-electricity-meters-4365" target="_blank">Klax 2.0</a>

The Klax 2.0 is a LoRaWAN indoor SML Opto Head for modern electricity meters

---

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Specifications

- indoor device
- Price ca. CHF 160.- (22.08.2023)
- Sensors/Devices/Interfaces
  - **IR Interface Protocols**
    - IEC 62056-21 B & C
    - SML 1.04
    - Logarex 
  - Expected life time: depending on usage, 3.5 years with a 15min sampling and 1h transmission
- Battery 1 x AA Lithium 3.0 Volt, 1500 mAh
- LoRaWAN version: 1.0.x
- LoRaWAN device class: A
- Protection: [IP20](https://en.wikipedia.org/wiki/IP_Code)
- Operating Temperature: 0 ... +60 °C
- Size: 96 × 35 × 40 mm

---

## Documents/Links
- [SML manual with payload description (2023-08-22)](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/alphaomega-klax2_02.pdf)
- [Payload decoder](https://github.com/Alpha-Omega-Technology/ttn-klax)

---

## Ordering Info
- Details: SML 1.04, Model Klax 2.0
- [Ordering Link](https://iot-shop.de/en/shop/klax-2-0-lorawan-sml-opto-head-for-modern-electricity-meters-4365)

---

## Device specific Information

### Electricity Meter Compatibility

Alpha-Omega Tech has tested the Klax with the following meters (2023-08-22)
- Holley DTZ541-ZEBA
- Holley DTZ541-ZDBA
- Logarex LK13BE803039, LK13BE803049
- Itron 3.HZ-AC-H4-A1, Itron 3.HZ-AC-D4-A1
- efr SGM-C4-2A920L, SGM-C4-2A92TL
- EMH eHZ-HW8E2A5L0EL1P, eHZ-GW8E2A500AX1
- Norax 1APA0194540255
- Landis+Gyr E320-AM1D.A1A.A0-S1B-S1
- Landis+Gyr E350, ZMD120AP, ZMD120AR, ZMB120, ZMD410
- ISKRA MT 681-D4A51-K0p
- FTL Tritschler VC2, VC3, TC2
- Elster AS3000
- UltraHeat 50 (Landis+Gyr, Siemens & neovac)
- eBZ DD3 BZ06 ETA - ODZ1
- DZG DVS74, DVS76, WS74

### LED States

| Blink code       | Meaning                               |
|------------------|---------------------------------------|
| 1 x 1s           | Initialisation completed              |
| 500ms (flashing) | Startup test running                  |
| 1 x 2s           | Startup test result IR & LoRa OK      |
| 2 x 200ms        | Startup test result IR OK, LoRa error |
| 3 x 200ms        | Startup test result IR error, LoRa OK |
| 4 x 200ms        | Startup test result IR & LoRa error   |

**Note**<br>
- If the start-up test fails, the blink code is repeated every 3 seconds for 30 seconds before the start-up test is repeated. This is repeated a maximum of 5 times before the sensor switches off.
- To restart the sensor afterwards, the battery must be removed for at least 2 minutes.

---

## Adding the Device to TTN
- Before a device can communicate via "The Things Network" we have to add it to an application.<br>

1. [Create a new application](https://hslu-ige-laes.github.io/lora-devices-ttn/docs/getting_started#create-a-new-application)
2. Under `Overview` click `(+) Register device`
3. Under `Input method` select `Select the end device in the LoRaWAN Device Repository`
4. Enter the following device information
   - `End device brand` select `Alpha-Omega Technology...`
   - `Model` select `KLAX`
   - `Hardware Ver.` select `2.0`
   - `Firmware` select `2.0`
	 - `Profile (Region)` select `EU_863_870`
5. **Important**: Under `Frequency plan` select `Europe 863-870 Mhz (SF12 for RX2)`
6. Under `JoinEUI` enter the `App EUI` from the App
7. Enter as well the `DevEUI` and the `AppKey` from the sticker
8. Set an end-device name
9. Press `Register end device`
10. Put in the battery
11. Place the KLAX without the cover on the electricity meter so that the LED display is visible (see https://hslu-ige-laes.github.io/lora-devices-ttn/docs/sensors/alphaomega-klax2#led-states)
12. After a successful test ( 1 x 2s flashing) you can put on the cover

- Now the device should be joined to the network and you can see the incoming telegrams in the `Live data` section
- The first data packets should now arrive here
- The data packets with port 3 are the data packets which show the power consumption
- The displayed code is the payload with the encrypted contents, under Fields are then the data such as the battery level ("batteryPerc"), the electricity meter type ("meterType") and the consumption data ("values")
- In the default configuration, the values are read out every 15 min are read out every 15 minutes and transmitted after 4 intervals
- The first reading values are available after about one hour
- The payload formatter should already be preset. If not, you can copy/paste it from below

---

## Payload formatter

```javascript
'use strict';

/*
  TTN decoder for KLAX LoRaWAN electricity meter sensors
  © Tobias Schramm 2020 (tobias.schramm@t-sys.eu) (licensed under CC BY-NC-SA 4.0)
*/

// Dump raw regsiter contents without decoding
var REGISTER_RAW = false;
// Enable debug console.log
var DEBUG = true;
// Use legacy output format, enables old output format for more modern Klax
var LEGACY_FORMAT = false;

function debug(msg) {
  if(DEBUG) {
    console.log('[DEBUG] ' + msg);
  }
}

function warning(msg) {
  console.log('[WARNING] ' + msg);
}

function error(msg) {
  console.log('[ERROR] ' + msg);
}

var SML_KLAX = "SML Klax";
var MODBUS_KLAX = "MODBUS Klax";

var KLAX_TYPES = [
  SML_KLAX,
  MODBUS_KLAX,
];

var METER_TYPES = [
  'SML',
  'IEC 62056-21 Mode B',
  'IEC 62056-21 Mode C',
  'Logarex',
  'eBZ',
  'Tritschler VC3',
];

var MODBUS_MODES = [
  'MODBUS RTU',
  'MODBUS ASCII',
  'MODBUS RTU INTERDELAY',
];

function parseHeader(data) {
  var version = (data[0] & 0xfc) >> 2;
  var deviceType = KLAX_TYPES[data[0] & 0x3];
  if (version > 0) {
    var batteryPerc = (data[1] & 0x7) * 20;
    var readMode = (data[1] & 0x38) >> 3;
  } else {
    var batteryPerc = (data[1] & 0xf) * 10;
    var readMode = (data[1] & 0x30) >> 4;
  }
  if (deviceType == SML_KLAX) {
    var meterType = METER_TYPES[readMode];
  } else {
    var meterType = MODBUS_MODES[readMode];
  }
  var configured = (data[1] & 0x40) > 0;
  var connTest = (data[1] & 0x80) > 0;
  return { 'version': version, 'deviceType': deviceType, 'batteryPerc': batteryPerc, 'meterType': meterType, 'configured': configured, 'connTest': connTest };
}

var REGISTER_UNITS = [
  'NDEF',
  'Wh',
  'W',
  'V',
  'A',
  'Hz',
  'varh',
  'var',
  'VAh',
  'VA',
];

function pow2(power) {
  return Math.pow(2, power);
}

function decodeUintN(data, bits, be) {
  var val = 0;
  var bytes = bits / 8;
  for(var i = 0; i < bytes; i++) {
    val += data[be ? bytes - 1 - i : i] * pow2(i * 8);
  }
  return val;
}

function decodeUint16BE(data) {
  return decodeUintN(data, 16, true);
}

function decodeUint32BE(data) {
  return decodeUintN(data, 32, true);
}

function decodeIntN(data, bits, be) {
  var val = 0;
  var bytes = bits / 8;
  for(var i = 0; i < bytes; i++) {
    val += data[i] << ((be ? (bytes - 1 - i) : i) * 8);
  }
  return val;
}

function decodeInt16BE(data) {
  return decodeIntN(data, 16, true);
}

function decodeInt32BE(data) {
  return decodeIntN(data, 32, true);
}

IEE754_FLOAT_MANTISSA_BITS = 23
IEE754_FLOAT_EXPONENT_BITS = 8

function decodeIEE754FloatBE(data) {
  var mantissa = data[3] | (data[2] << 8) | ((data[1] & 0x7f) << 16);
  var exponent = ((data[1] & 0x80) >> 7) | ((data[0] & 0x7f) << 1);
  var sign = Math.pow(-1, (data[0] & 0x80) >> 7);
  var exception = exponent == pow2(IEE754_FLOAT_EXPONENT_BITS) - 1;
  if (exception) {
    if (mantissa > 0) {
      return NaN;
    } else {
      return sign * Infinity;
    }
  }

  var normalized = exponent > 0;
  if (normalized) {
    // Remove exponent bias
    exponent -= pow2(IEE754_FLOAT_EXPONENT_BITS - 1) - 1;
    return sign * (pow2(exponent) + mantissa * pow2(exponent - IEE754_FLOAT_MANTISSA_BITS));
  } else {
    exponent = -(pow2(IEE754_FLOAT_EXPONENT_BITS - 1) - 2);
    return sign * (mantissa * pow2(exponent - IEE754_FLOAT_MANTISSA_BITS));
  }
}

IEE754_DOUBLE_MANTISSA_BITS = 52
IEE754_DOUBLE_EXPONENT_BITS = 11

function decodeIEE754DoubleBE(data) {
  var mantissa = data[7] | (data[6] << 8) | (data[5] << 16) | (data[4] << 24);
  // Bitops are 32 bit in js, use arithmetics for more than 32 bits
  mantissa += (data[3] | (data[2] << 8) | ((data[1] & 0xf) << 16)) * pow2(32);
  var exponent = ((data[1] & 0xf0) >> 4) | ((data[0] & 0x7f) << 4);
  var sign = Math.pow(-1, (data[0] & 0x80) >> 7);
  var exception = exponent == pow2(IEE754_DOUBLE_EXPONENT_BITS) - 1;
  if (exception) {
    if (mantissa > 0) {
      return NaN;
    } else {
      return sign * Infinity;
    }
  }

  var normalized = exponent > 0;
  if (normalized) {
    // Remove exponent bias
    exponent -= pow2(IEE754_DOUBLE_EXPONENT_BITS - 1) - 1;
    return sign * (pow2(exponent) + mantissa * pow2(exponent - IEE754_DOUBLE_MANTISSA_BITS));
  } else {
    exponent = -(pow2(IEE754_DOUBLE_EXPONENT_BITS - 1) - 2);
    return sign * (mantissa * pow2(exponent - IEE754_DOUBLE_MANTISSA_BITS));
  }
}

function mkRegister(data, lastValid, unitId, valueDecoder) {
  var unit = unitId < REGISTER_UNITS.length ? REGISTER_UNITS[unitId] : null;
  var dataValid = false;
  var values = [ ];
  while(data.length >= 4) {
    if(REGISTER_RAW) {
      var raw = data.slice(0, 4);
      var bytes = [ ];
      for(var i = 0; i < raw.length; i++) {
        var val = raw[i];
        if(val != 0) {
          dataValid = true;
        }
        bytes.push(parseInt(val));
      }
      values.push(bytes);
    } else {
      var val = valueDecoder(data);
      if(val != 0) {
        dataValid = true;
      }
      values.push(val);
    }
    data = data.slice(4);
  }
  dataValid = dataValid || lastValid;
  return { 'data_valid': dataValid, 'dataValid': dataValid, 'unit': unit, 'values': values };
}

function decodeHistoric(data) {
  var regmask = data[0];
  var reg1Active = (regmask & 0x01) > 0;
  var reg1Filter = (regmask & 0x06) >> 1;
  var reg1Valid = (regmask & 0x08) > 0;
  var reg2Active = (regmask & 0x10) > 0;
  var reg2Filter = (regmask & 0x60) >> 5;
  var reg2Valid = (regmask & 0x80) > 0;
  var units = data[1];
  var reg1Unit = units & 0x0f;
  var reg2Unit = (units & 0xf0) >> 4;
  data = data.slice(2);
  var registers = [ ];
  if(reg1Active) {
    var reg = mkRegister(data.slice(0, 16), reg1Valid, reg1Unit, decodeInt32BE);
    reg.filterId = reg1Filter;
    registers.push(reg);
  }
  data = data.slice(16);
  if(reg2Active) {
    var reg = mkRegister(data.slice(0, 16), reg2Valid, reg2Unit, decodeInt32BE);
    reg.filterId = reg2Filter;
    registers.push(reg);
  }
  return { 'type': 'historic', 'registers': registers };
}

function decodeFilter(data) {
  var filterActive = (data[0] & 0x1) > 0;
  var filterId = (data[0] & 0x6) >> 1;
  var unitId = (data[0] & 0xf0) >> 4;
  var dataValid = (data[1] & 0xf);
  data = data.slice(2);
  var values = [ ];
  if (LEGACY_FORMAT) {
    var registers = [ mkRegister(data, dataValid > 0, unitId, decodeIEE754FloatBE) ];
    return { 'type': 'historic', 'registers': registers };
  } else {
    var filterUnit = REGISTER_UNITS[unitId];
    for (var i = 0; i < 4; i++) {
      var value = decodeIEE754FloatBE(data);
      var valid = (dataValid & (1 << i)) > 0;
      values.push({ 'value': value, 'valid': valid });
      data = data.slice(4);
    }
    return { 'type': 'filter', 'register': { 'filterActive': filterActive, 'filterId': filterId, 'unit': filterUnit, 'values': values } };
  }
}

function decodeNow(data, valueDecoder) {
  var registers = [ ];
  for(var i = 0; i < 4; i++) {
    var filterSet = (data[0] & (1<<i)) > 0;
    var filterValid = (data[0] & (1<<(i + 4))) > 0;
    var unitReg = data[i >= 2 ? 2 : 1];
    var unitId = (unitReg & (i % 2 == 0 ? 0x0f : 0xf0)) >> ((i % 2) * 4);
    var reg = mkRegister(data.slice(3 + (4 * i), 3 + (4 * (i + 1))), filterValid, unitId, valueDecoder);
    reg.filterSet = filterSet;
    reg.filterValid = filterValid;
    registers.push(reg);
  }
  return { 'type': 'now', 'registers': registers };
}

function decodeNowInt32(data) {
  return decodeNow(data, decodeInt32BE);
}

function decodeNowFloat(data) {
  return decodeNow(data, decodeIEE754FloatBE);
}

function uint8ToHex(val) {
  var hex = val.toString(16);
  if(hex.length < 2) {
    hex = '0' + hex;
  }
  return hex;
}

function decodeServerID(data) {
  var id = '';
  for(var i = 0; i < data.length; i++) {
    id = id + uint8ToHex(data[i]);
  }
  return { 'type': 'serverID', 'id': id };
}

var MODBUS_FILTER_TYPES = [
  { 'name': "DOUBLE", 'decode': decodeIEE754DoubleBE },
  { 'name': "INT16", 'decode': decodeInt16BE },
  { 'name': "UINT16", 'decode': decodeUint16BE },
  { 'name': "INT32", 'decode': decodeInt32BE },
  { 'name': "UINT32", 'decode': decodeUint32BE },
  { 'name': "FLOAT", 'decode': decodeIEE754FloatBE },
];

function decodeModbusFilter(data, len) {
  var filterActive = (data[0] & 0x1) > 0;
  var filterId = (data[0] & 0x6) >> 1;
  var filterType = MODBUS_FILTER_TYPES[(data[0] & 0xf0) >> 4];
  var registerValid = (data[1] & 0xf);
  data = data.slice(2);
  values = [ ];
  var i = 0;
  while (data.length >= len) {
    values.push({ 'valid': (registerValid & (1 << i)) > 0, 'value': filterType.decode(data) });
    data = data.slice(len);
    i++;
  }

  return { 'type': 'registerFilter', 'register': { 'filterId': filterId, 'filterActive': filterActive, 'filterType': filterType.name, 'values': values } };
}

function decodeModbusFilter2Byte(data) {
  return decodeModbusFilter(data, 2);
}

function decodeModbusFilter4Byte(data) {
  return decodeModbusFilter(data, 4);
}

function decodeModbusFilter8Byte(data) {
  return decodeModbusFilter(data, 8);
}

function decodeDeviceID(data) {
  var id = decodeUint32BE(data);
  return { 'type': 'deviceID', 'id': id };
}

function decodeModbusRegisterStatus(data) {
  var status = data[0];
  var filters = [];
  for (var i = 0; i < 4; i++) {
    filters.push({ 'set': (status & (1 << i)) > 0, 'valid': (status & (1 << (4 + i))) > 0 });
  }
  return { 'type': 'modbusRegisterStatus', 'filters': filters };
}

var PAYLOAD_HANDLERS = [
  { 'id': 1, 'len': 34, 'decode': decodeHistoric, 'version': 0 },
  { 'id': 1, 'len': 18, 'decode': decodeFilter },
  { 'id': 2, 'len': 19, 'decode': decodeNowInt32, 'version': 0 },
  { 'id': 2, 'len': 19, 'decode': decodeNowFloat },
  { 'id': 3, 'len': 10, 'decode': decodeServerID },
  { 'id': 4, 'len': 10, 'decode': decodeModbusFilter2Byte },
  { 'id': 5, 'len': 18, 'decode': decodeModbusFilter4Byte },
  { 'id': 6, 'len': 34, 'decode': decodeModbusFilter8Byte },
  { 'id': 7, 'len': 1, 'decode': decodeModbusRegisterStatus },
  { 'id': 8, 'len': 4, 'decode': decodeDeviceID },
];

function getHandler(data, version) {
  var id = data[0];
  for(var i = 0; i < PAYLOAD_HANDLERS.length; i++) {
    var handler = PAYLOAD_HANDLERS[i];
    if(handler.id == id && (handler['version'] == undefined || handler.version == version)) {
      return handler;
    }
  }
  return null;
}

function parsePayload(handler, data) {
  return handler.decode(data.slice(0, handler.len));
}

function parseMsgInfo(data) {
  var msgIdx = data[0];
  var msgCnt = data[1] & 0x0f;
  var msgNum = (data[1] & 0xf0) >> 4;
  return { 'msgIdx': msgIdx, 'msgCnt': msgCnt, 'msgNum': msgNum };
}

function parseApp(data) {
  var header = parseHeader(data);
  data = data.slice(2);
  var msgInfo = parseMsgInfo(data);
  data = data.slice(2);
  debug('Got ' + data.length + ' bytes of payload');
  var payloads = [ ];
  while(data.length > 0) {
    var handler = getHandler(data, header.version);
    if(!handler) {
      debug('Encountered unknown payload type ' + data[0])
      break;
    }
    data = data.slice(1);
    debug('Found payload type ' + handler.id + ' with length of ' + handler.len + ' bytes');
    payloads.push(parsePayload(handler, data));
    data = data.slice(handler.len);
  }
  var appData = { 'type': 'app', 'header': header, 'msgInfo': msgInfo, 'payloads': payloads };
  return appData;
}

function parseConfig(data) {
  var header = parseHeader(data);
  data = data.slice(2);
  var measureInterval = decodeUint16BE(data);
  return { 'type': 'config', 'header': header, 'measureIntervalMin': measureInterval };
}

function parseInfo(data) {
  var header = parseHeader(data);
  data = data.slice(2);
  var appMajorVersion = data[0];
  var appMinorVersion = data[1];
  return { 'type': 'info', 'header': header, 'appMajorVersion': appMajorVersion, 'appMinorVersion': appMinorVersion };
}

function parseRegisterDefs(data) {
  var registers = [ ];
  while(data.length >= 3) {
    var main = data[0];
    var major = data[1];
    var minor = data[2];
    registers.push({ 'main': main, 'major': major, 'minor': minor });
    data = data.slice(3);
  }
  return registers;
}

var MODBUS_READ_FUNCTION_CODES = [
  'Read Coils',
  'Read Discrete Inputs',
  'Read Holding Registers',
  'Read Input Registers',
];

function parseRegisterDefsModbus(data) {
  var registers = [ ];
  while(data.length >= 3) {
    var registerAddress = decodeUint16BE(data)
    var functionCode = MODBUS_READ_FUNCTION_CODES[data[2] & 0x3];
    var filterType = MODBUS_FILTER_TYPES[(data[2] & 0xf0) >> 4];
    registers.push({ 'registerAddress': registerAddress, 'functionCode': functionCode, 'variableType': filterType.name });
    data = data.slice(3);
  }
  return registers;
}

function parseRegisterSearch(data) {
  var header = parseHeader(data);
  data = data.slice(2);
  var msgInfo = parseMsgInfo(data);
  data = data.slice(2);
  return { 'type': 'registerSearch', 'header': header, 'msgInfo': msgInfo, 'registerDefs': parseRegisterDefs(data) };
}

function parseRegisterSet(data) {
  var header = parseHeader(data);
  data = data.slice(2);
  var activeFilters = data[0] & 0x0f;
  data = data.slice(1);
  var filters = parseRegisterDefs(data.slice(0, 12));
  for(var i = 0; i < filters.length; i++) {
    filters[i].active = ((activeFilters & (1<<i)) >> i) > 0;
  }
  return { 'type': 'registerSet', 'header': header, 'filters': filters };
}

var MODBUS_PARITIES = [
  'NONE',
  'ODD',
  'EVEN',
  'NONE SPECIAL',
];

function parseModbusSet(data) {
  var header = parseHeader(data);
  if (header.deviceType != MODBUS_KLAX) {
    error("Invalid payload, modbusSet can only be used with MODBUS Klax");
    return null;
  }
  data = data.slice(2);
  var modbusAddr = data[0];
  data = data.slice(1);
  var modbusBaud = decodeUint16BE(data);
  data = data.slice(2);
  var modbusMode = MODBUS_MODES[data[0] & 0x3];
  var modbusParity = MODBUS_PARITIES[(data[0] & 0x30) >> 4];
  data = data.slice(1);
  var modbusReadRetries = data[0] & 0xf;
  var modbusReadTimeoutMilliseconds = ((data[0] & 0xf0) >> 4) * 100;
  data = data.slice(1);
  var modbusWriteBeforeRead = (data[0] & 0x1) > 0;
  var modbusWaitBeforeReadMilliseconds = ((data[0] & 0xf0) >> 4) * 100;
  data = data.slice(1);
  var modbusWriteAddr = decodeUint16BE(data);
  data = data.slice(2);
  var modbusWriteData = decodeUint16BE(data);
  data = data.slice(2);
  var activeFilters = data[0] & 0x0f;
  data = data.slice(1);
  var filters = parseRegisterDefsModbus(data.slice(0, 12));
  for(var i = 0; i < filters.length; i++) {
    filters[i].active = ((activeFilters & (1<<i)) >> i) > 0;
  }
  return { 'type': 'modbusSet', 'header': header, 'modbus': { 'address': modbusAddr, 'baud': modbusBaud, 'mode': modbusMode,
           'parity': modbusParity, 'readRetries': modbusReadRetries, 'readTimeoutMilliseconds': modbusReadTimeoutMilliseconds,
           'writeBeforeRead': modbusWriteBeforeRead, 'waitBeforeReadMilliseconds': modbusWaitBeforeReadMilliseconds,
           'writeAddress': modbusWriteAddr, 'writeData': modbusWriteData }, 'filters': filters };
}

var DECODERS = [
  { 'port': 3,   minLen:  4, name: 'app',            'decode': parseApp },
  { 'port': 100, minLen:  4, name: 'config',         'decode': parseConfig },
  { 'port': 101, minLen:  4, name: 'info',           'decode': parseInfo },
  { 'port': 103, minLen:  4, name: 'registerSearch', 'decode': parseRegisterSearch },
  { 'port': 104, minLen: 15, name: 'registerSet',    'decode': parseRegisterSet },
  { 'port': 105, minLen: 25, name: 'modbusSet',      'decode': parseModbusSet },
];

function getDecoder(port) {
  for(var i = 0; i < DECODERS.length; i++) {
    var decoder = DECODERS[i];
    if(decoder.port == port) {
      return decoder;
    }
  }
  return null;
}

function Decoder(bytes, port) {
  var decoder = getDecoder(port);
  if(!decoder) {
    warning('No decoder for port ' + port + ' found');
    return { 'port': port, 'rawData': bytes };
  }
  if(bytes.len < decoder.minLen) {
    warning('Message too short for decoder "' + decoder.name + '", got ' + bytes.len + ' bytes need at least ' + decoder.minLen + ' bytes');
    return { 'port': port, 'rawData': bytes };
  }
  return decoder.decode(bytes);
}
```