---
layout: default
title: WATTECO - Flash O
parent: Sensors
---

<img src="https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/watteco-flash-o_01.jpg" width="250" align="right" class="inline"/>

# WATTECO - Flash O
{: .no_toc }

- Manufacturer: <a href="http://www.watteco.com/" target="_blank">WATTECO</a>
- Product: <a href="https://www.watteco.com/product/flasho-sensor-lorawan/" target="_blank">Flash'O</a>

The Flash'O is a LoRaWAN meter interface that transmits the accumulated number of LED flashes from any pulse meter, including water, gas, electricity, and energy meters.

The counter value can later be converted into an energy unit, such as kilowatt-hours (kWh), within the dashboard or during data analysis. The conversion depends on the number of impulses per kWh, which varies by meter model.

---

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Specifications
- Price ca. CHF 183.- (15.07.2020)
- Sensors:
  - **Optical Probe**: Compatible with meters emitting LED pulses up to 500 pulses per second
- Cable length approx. 0.5m
- Protection: [IP55](https://en.wikipedia.org/wiki/IP_Code)
- Power Supply: 1 battery, 3.6 V, 3600mAh, Lithium
  - Expected life time: up to 12 years with 1 telegram per day
	- Up to 8 years with one measurement per hour and one transmission per day
- LoRaWAN Version 1.0.2 Region Parameter rev B
- LoRaWAN Device Class Class A
- Protection Rating IP55
- Operating Temperature -20°C to +50°C
- Dimensions 84 x 82 x 85 mm

---

## Documents
  - [Payload description from support.watteco.com (2020-07-15)](http://support.watteco.com/flasho/#HumanMachineInterface)
  - [Datasheet from watteco.fr (2020-07-15)](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/watteco-flash-o_02.pdf)
	- [Quick Start Guide from watteco.fr (2025-04-09)](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/watteco-flash-o_06.pdf)
  - [Online Device Support Page](https://support.nke-watteco.com/flasho/)

---

## Ordering Info
- [Ordering Link](https://smartmakers.io/en/iot-sensors-devices-overview/)
- Product Ref 50-70-071 (FLASH’O with sensor - mounted in factory with 0.5m cable)

---

## Button Actions, Modes and LED States
The device does not have a physical button, but a magnetic contact. Hold a magnet to the device wall where the sticker `ILS` is.

| Action               | Description                    | LED Status       |
|----------------------|--------------------------------|------------------|
| Hold magnet > 1s on ILS sticker    | Power on and join network      | 1. LED will flash red during this 1s period <br>2. Waiting to join LoRa network: LED blinks shortly red every 5s <br>3. Join LoRa network success: green LED solid on for 3s |
| Hold magnet > 5s on ILS sticker     | Power off           | 1. LED will flash red during this 5s period<br>2. Before device turns of it flashes red 5 times in 3s      |
| Pass magnet over ILS sticker     | Switch on/off configuration mode  | 1. The LED blinks  red (3s off, 3s on) while in config mode <br>2. The device sends an uplink frame every minute. <br>3. The configuration mode lasts 10 minutes |
| Quick passage and long hold over ILS sticker     | Factory reset  | All the applicative settings gets deleted and set to factory default |

---

## Compatibility of Electricity Meters
The device was successfully tested with the following products.

- **Landis & Gyr ZMD120AP** (1'000 impulses per kWh)<br>
  <img src="https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/watteco-flash-o_04.png" width="250"><br>
  - Place the sensor over the LED (yellow colored)

- **Kamstrup DK-8660 Omnipower** (1'000 impulses per kWh)<br>
  <img src="https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/watteco-flash-o_05.png" width="250"><br>
  - Place the sensor over the LED (yellow colored)

---

## Installation & Placement Guidelines
To ensure optimal sensor performance
- minimize the number of physical obstacles between the device and the LoRaWAN gateway
- place the sensor as far as possible away from electrical cables
- mount the sensor as high as possible
- ensure that the cable gland is oriented downward to have the antenna oriented correctly

---

## Adding the Device to TTN
- The supplier should give you a csv file where you can find the `DevEUI`, `AppEUI` and `AppKey`<br>

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
10. Now put a magnet longer than 1s on the `ILS` sticker and the device should switch on
11. The device should log in and you should see in TTN a green circle as `Status` in the tab `Device Overview`.
   - if not, please wait several hours and check again. The first attempt might take a while.
12. Change the sampling rate and battery reporting, as described below. Per default the device only sends one message a day!

- Now the device should join the network and you can see the incoming telegrams in the `Live data` section

---

## Optional settings

### Change sampling rate and battery reporting
- The Flash'O sends per default a telegram once a day with the counter value.<br>
- If you want to change that you have to send a telegram to the device. To do so follow the instructions below.<br>

1. In TTN, select the device in `End devices` and change to the tab `Messaging`, select `Schedule Downlink`
2. Put the device in `Configuration Mode` (see table above). In this mode, the device sends a telegram every minute and opens a window for receiving Downlink messages for configuration.
3. Change the `FPort` to `125`
4. Copy/paste the following payloads step by step into the `Payload` field and press `Schedule downlink`. Open a second tab with the `Live data` view, to see whether the command got transmitted.
   - deactivate standard report: `11 06 00 0f 00 04 02 23 00 00 00 00 00 00 00 00`
	 - activate battery report every 24 hours or 0.1V at min 10 minutes: `11 06 00 50 15 00 06 04 80 0a 85 a0 00 64 00 64 09`
	 - activate batch report at least every 15min samples or 200 impulses: `11 06 00 0f 1d 04 02 00 00 00 80 0f 00 00 00 c8 00 00 00 01 01`

- For details and other configurations see [Frame Examples](http://support.watteco.com/flasho/#FrameExamples)

---

## Payload formatter

{% raw %}
```javascript
/*
 * JavaScript implementation of brUncompress.
 */

// {{{ Constants

var ST_UNDEF = 0
var ST_BL = 1
var ST_U4 = 2
var ST_I4 = 3
var ST_U8 = 4
var ST_I8 = 5
var ST_U16 = 6
var ST_I16 = 7
var ST_U24 = 8
var ST_I24 = 9
var ST_U32 = 10
var ST_I32 = 11
var ST_FL = 12

var ST = {}
ST[ST_UNDEF] = 0
ST[ST_BL] = 1
ST[ST_U4] = 4
ST[ST_I4] = 4
ST[ST_U8] = 8
ST[ST_I8] = 8
ST[ST_U16] = 16
ST[ST_I16] = 16
ST[ST_U24] = 24
ST[ST_I24] = 24
ST[ST_U32] = 32
ST[ST_I32] = 32
ST[ST_FL] = 32

var BR_HUFF_MAX_INDEX_TABLE = 14
var NUMBER_OF_SERIES = 16

var HUFF = [
  [
    { sz: 2, lbl: 0x000 },
    { sz: 2, lbl: 0x001 },
    { sz: 2, lbl: 0x003 },
    { sz: 3, lbl: 0x005 },
    { sz: 4, lbl: 0x009 },
    { sz: 5, lbl: 0x011 },
    { sz: 6, lbl: 0x021 },
    { sz: 7, lbl: 0x041 },
    { sz: 8, lbl: 0x081 },
    { sz: 10, lbl: 0x200 },
    { sz: 11, lbl: 0x402 },
    { sz: 11, lbl: 0x403 },
    { sz: 11, lbl: 0x404 },
    { sz: 11, lbl: 0x405 },
    { sz: 11, lbl: 0x406 },
    { sz: 11, lbl: 0x407 }
  ],
  [
    { sz: 7, lbl: 0x06f },
    { sz: 5, lbl: 0x01a },
    { sz: 4, lbl: 0x00c },
    { sz: 3, lbl: 0x003 },
    { sz: 3, lbl: 0x007 },
    { sz: 2, lbl: 0x002 },
    { sz: 2, lbl: 0x000 },
    { sz: 3, lbl: 0x002 },
    { sz: 6, lbl: 0x036 },
    { sz: 9, lbl: 0x1bb },
    { sz: 9, lbl: 0x1b9 },
    { sz: 10, lbl: 0x375 },
    { sz: 10, lbl: 0x374 },
    { sz: 10, lbl: 0x370 },
    { sz: 11, lbl: 0x6e3 },
    { sz: 11, lbl: 0x6e2 }
  ],
  [
    { sz: 4, lbl: 0x009 },
    { sz: 3, lbl: 0x005 },
    { sz: 2, lbl: 0x000 },
    { sz: 2, lbl: 0x001 },
    { sz: 2, lbl: 0x003 },
    { sz: 5, lbl: 0x011 },
    { sz: 6, lbl: 0x021 },
    { sz: 7, lbl: 0x041 },
    { sz: 8, lbl: 0x081 },
    { sz: 10, lbl: 0x200 },
    { sz: 11, lbl: 0x402 },
    { sz: 11, lbl: 0x403 },
    { sz: 11, lbl: 0x404 },
    { sz: 11, lbl: 0x405 },
    { sz: 11, lbl: 0x406 },
    { sz: 11, lbl: 0x407 }
  ]
]

// }}}

// {{{ Polyfills
Math.trunc =
  Math.trunc ||
  function(x) {
    if (isNaN(x)) {
      return NaN
    }
    if (x > 0) {
      return Math.floor(x)
    }
    return Math.ceil(x)
  }
// }}}

/**
 * brUncompress main function
 */
function brUncompress(tagsz, argList, hexString, batch_absolute_timestamp) {
  var out = initResult()
  var buffer = createBuffer(parseHexString(hexString))
  var flag = generateFlag(buffer.getNextSample(ST_U8))

  out.batch_counter = buffer.getNextSample(ST_U8, 3)
  buffer.getNextSample(ST_U8, 1)

  var temp = prePopulateOutput(out, buffer, argList, flag, tagsz)
  var last_timestamp = temp.last_timestamp
  var index_of_the_first_sample = temp.index_of_the_first_sample

  if (flag.hasSample) {
    last_timestamp = uncompressSamplesData(
      out,
      buffer,
      index_of_the_first_sample,
      argList,
      last_timestamp,
      flag,
      tagsz
    )
  }

  out.batch_relative_timestamp = extractTimestampFromBuffer(
    buffer,
    last_timestamp
  )
  return adaptToExpectedFormat(out, argList, batch_absolute_timestamp)
}

/////////////// Sub functions ///////////////

/**
 * Init br_uncompress result data structure
 */
function initResult() {
  var series = [],
    i = 0
  while (i < NUMBER_OF_SERIES) {
    series.push({
      codingType: 0,
      codingTable: 0,
      resolution: null,
      uncompressSamples: []
    })
    i += 1
  }
  return {
    batch_counter: 0,
    batch_relative_timestamp: 0,
    series: series
  }
}

/**
 * Function to create a buffer from a byteArray. Allow to read sample from the
 * byteArray to extract data.
 */
function createBuffer(byteArray) {
  /**
   * Retrieve the pattern for HUFF table lookup
   */
  function bitsBuf2HuffPattern(byteArray, index, nb_bits) {
    var sourceBitStart = index
    var sz = nb_bits - 1
    if (byteArray.length * 8 < sourceBitStart + nb_bits) {
      throw "Verify that dest buf is large enough"
    }
    var bittoread = 0
    var pattern = 0
    while (nb_bits > 0) {
      if (byteArray[sourceBitStart >> 3] & (1 << (sourceBitStart & 0x07))) {
        pattern |= 1 << (sz - bittoread)
      }
      nb_bits--
      bittoread++
      sourceBitStart++
    }
    return pattern
  }

  return {
    index: 0,
    byteArray: byteArray,
    getNextSample: function(sampleType, nbBitsInput) {
      var nbBits = nbBitsInput || ST[sampleType]
      var sourceBitStart = this.index
      this.index += nbBits
      if (sampleType === ST_FL && nbBits !== 32) {
        throw "Mauvais sampletype"
      }

      var u32 = 0
      var nbytes = Math.trunc((nbBits - 1) / 8) + 1
      var nbitsfrombyte = nbBits % 8
      if (nbitsfrombyte === 0 && nbytes > 0) {
        nbitsfrombyte = 8
      }

      while (nbytes > 0) {
        var bittoread = 0
        while (nbitsfrombyte > 0) {
          var idx = sourceBitStart >> 3
          if (this.byteArray[idx] & (1 << (sourceBitStart & 0x07))) {
            u32 |= 1 << ((nbytes - 1) * 8 + bittoread)
          }
          nbitsfrombyte--
          bittoread++
          sourceBitStart += 1
        }
        nbytes--
        nbitsfrombyte = 8
      }
      // Propagate the sign bit if 1
      if (
        (sampleType == ST_I4 || sampleType == ST_I8 ||sampleType == ST_I16 || sampleType == ST_I24) &&
        u32 & (1 << (nbBits - 1))
      ) {
        for (var i = nbBits; i < 32; i++) {
          u32 |= 1 << i
          nbBits++
        }
      }
      return u32
    },

    /**
     * Extract sz and bi from Huff table
     */
    getNextBifromHi: function(huff_coding) {
      for (var i = 2; i < 12; i++) {
        var lhuff = bitsBuf2HuffPattern(this.byteArray, this.index, i)
        for (var j = 0; j < HUFF[huff_coding].length; j++) {
          if (
            HUFF[huff_coding][j].sz == i &&
            lhuff == HUFF[huff_coding][j].lbl
          ) {
            this.index += i
            return j
          }
        }
      }
      throw "Bi not found in HUFF table"
    }
  }
}

/**
 * Convert the hex string given as parameter to a ByteArray
 */
function parseHexString(str) {
  str = str
    .split("")
    .filter(function(x) {
      return !isNaN(parseInt(x, 16))
    })
    .join("")
  var result = []
  while (str.length >= 2) {
    result.push(parseInt(str.substring(0, 2), 16))
    str = str.substring(2, str.length)
  }
  return result
}

/**
 * Generate a flag object from an integer value.
 */
function generateFlag(flagAsInt) {
  var binbase = flagAsInt.toString(2)

  // leftpad
  while (binbase.length < 8) {
    binbase = "0" + binbase
  }

  return {
    isCommonTimestamp: parseInt(binbase[binbase.length - 2], 2),
    hasSample: !parseInt(binbase[binbase.length - 3], 2),
    batch_req: parseInt(binbase[binbase.length - 4], 2),
    nb_of_type_measure: parseInt(binbase.substring(0, 4), 2)
  }
}

/**
 * Prepopulate output with relative timestamp and measure of the first sample
 * for each series.
 */
function prePopulateOutput(out, buffer, argList, flag, tagsz) {
  var currentTimestamp = 0
  var index_of_the_first_sample = 0
  for (var i = 0; i < flag.nb_of_type_measure; i++) {
    var tag = {
      size: tagsz,
      lbl: buffer.getNextSample(ST_U8, tagsz)
    }
    var sampleIndex = findIndexFromArgList(argList, tag)

    if (i == 0) {
      index_of_the_first_sample = sampleIndex
    }

    currentTimestamp = extractTimestampFromBuffer(buffer, currentTimestamp)
    out.series[sampleIndex] = computeSeries(
      buffer,
      argList[sampleIndex].sampletype,
      tag.lbl,
      currentTimestamp
    )
    if (flag.hasSample) {
      out.series[sampleIndex].codingType = buffer.getNextSample(ST_U8, 2)
      out.series[sampleIndex].codingTable = buffer.getNextSample(ST_U8, 2)
    }
  }
  return {
    last_timestamp: currentTimestamp,
    index_of_the_first_sample: index_of_the_first_sample
  }
}

/**
 * Initialize next series from buffer
 */
function computeSeries(buffer, sampletype, label, currentTimestamp) {
  return {
    uncompressSamples: [
      {
        data_relative_timestamp: currentTimestamp,
        data: {
          value: getMeasure(buffer, sampletype),
          label: label
        }
      }
    ],
    codingType: 0,
    codingTable: 0,
    resolution: null
  }
}

/**
 * Return the index of tag lbl in the argument list
 */
function findIndexFromArgList(argList, tag) {
  for (var i = 0; i < argList.length; i++) {
    if (argList[i].taglbl === tag.lbl) {
      return i
    }
  }
  throw "Cannot find index in argList"
}

/**
 * Extract a new time stamp using Huff table, optionnaly from a baseTimestamp
 */
function extractTimestampFromBuffer(buffer, baseTimestamp) {
  if (baseTimestamp) {
    var bi = buffer.getNextBifromHi(1)
    return computeTimestampFromBi(buffer, baseTimestamp, bi)
  }
  return buffer.getNextSample(ST_U32)
}

/**
 * Compute a new timestamp from a previous one, regarding bi value
 */
function computeTimestampFromBi(buffer, baseTimestamp, bi) {
  if (bi > BR_HUFF_MAX_INDEX_TABLE) {
    return buffer.getNextSample(ST_U32)
  }
  if (bi > 0) {
    return computeTimestampFromPositiveBi(buffer, baseTimestamp, bi)
  }
  return baseTimestamp
}

/**
 * Compute a new timestamp from a previous one, regarding posotive bi value
 */
function computeTimestampFromPositiveBi(buffer, baseTimestamp, bi) {
  return buffer.getNextSample(ST_U32, bi) + baseTimestamp + Math.pow(2, bi) - 1
}

/**
 * Extract the measure from the buffer, handling float case
 */

function getMeasure(buffer, sampletype) {
  var v = buffer.getNextSample(sampletype)
  return sampletype === ST_FL ? bytes2Float32(v) : v
}

/**
 * Convert bytes to a float32 representation.
 */
function bytes2Float32(bytes) {
  var sign = bytes & 0x80000000 ? -1 : 1,
    exponent = ((bytes >> 23) & 0xff) - 127,
    significand = bytes & ~(-1 << 23)

  if (exponent == 128) {
    return sign * (significand ? Number.NaN : Number.POSITIVE_INFINITY)
  }

  if (exponent == -127) {
    if (significand == 0) {
      return sign * 0.0
    }
    exponent = -126
    significand /= 1 << 22
  } else {
    significand = (significand | (1 << 23)) / (1 << 23)
  }

  return sign * significand * Math.pow(2, exponent)
}

/**
 * Uncompress samples data presenting common timestamp or separate timestamp
 */
function uncompressSamplesData(
  out,
  buffer,
  index_of_the_first_sample,
  argList,
  last_timestamp,
  flag,
  tagsz
) {
  if (flag.isCommonTimestamp) {
    return handleCommonTimestamp(
      out,
      buffer,
      index_of_the_first_sample,
      argList,
      flag,
      tagsz
    )
  }
  return handleSeparateTimestamp(
    out,
    buffer,
    argList,
    last_timestamp,
    flag,
    tagsz
  )
}

/**
 * Uncompress data in case of common timestamp
 */
function handleCommonTimestamp(
  out,
  buffer,
  index_of_the_first_sample,
  argList,
  flag,
  tagsz
) {
  //number of sample
  var nb_sample_to_parse = buffer.getNextSample(ST_U8, 8)
  var tag = {}

  var temp = initTimestampCommonTable(
    out,
    buffer,
    nb_sample_to_parse,
    index_of_the_first_sample
  )
  var timestampCommon = temp.timestampCommon
  var lastTimestamp = temp.lastTimestamp

  for (var j = 0; j < flag.nb_of_type_measure; j++) {
    var first_null_delta_value = 1
    tag.lbl = buffer.getNextSample(ST_U8, tagsz)
    var sampleIndex = findIndexFromArgList(argList, tag)
    for (var i = 0; i < nb_sample_to_parse; i++) {
      //Available bit
      var available = buffer.getNextSample(ST_U8, 1)
      if (available) {
        //Delta value
        var bi = buffer.getNextBifromHi(out.series[sampleIndex].codingTable)
        var currentMeasure = {
          data_relative_timestamp: 0,
          data: {}
        }
        if (bi <= BR_HUFF_MAX_INDEX_TABLE) {
          var precedingValue =
            out.series[sampleIndex].uncompressSamples[
              out.series[sampleIndex].uncompressSamples.length - 1
            ].data.value
          if (bi > 0) {
            currentMeasure.data.value = completeCurrentMeasure(
              buffer,
              precedingValue,
              out.series[sampleIndex].codingType,
              argList[sampleIndex].resol,
              bi
            )
          } else {
            // (bi <= 0)
            if (first_null_delta_value) {
              // First value is yet recorded starting from the header
              first_null_delta_value = 0
              continue
            } else {
              currentMeasure.data.value = precedingValue
            }
          }
        } else {
          // bi > BR_HUFF_MAX_INDEX_TABLE
          currentMeasure.data.value = buffer.getNextSample(
            argList[sampleIndex].sampletype
          )
        }
        currentMeasure.data_relative_timestamp = timestampCommon[i]
        out.series[sampleIndex].uncompressSamples.push(currentMeasure)
      }
    }
  }
  return lastTimestamp
}

/**
 * Initialize common timestamp table. Returns the table and last calculated timestamp
 */
function initTimestampCommonTable(
  out,
  buffer,
  nbSampleToParse,
  firstSampleIndex
) {
  var timestampCommon = []
  var lastTimestamp = 0
  var timestampCoding = buffer.getNextSample(ST_U8, 2)
  for (var i = 0; i < nbSampleToParse; i++) {
    //delta timestamp
    var bi = buffer.getNextBifromHi(timestampCoding)
    if (bi <= BR_HUFF_MAX_INDEX_TABLE) {
      if (i == 0) {
        timestampCommon.push(
          out.series[firstSampleIndex].uncompressSamples[0]
            .data_relative_timestamp
        )
      } else {
        if (bi > 0) {
          var precedingTimestamp = timestampCommon[i - 1]
          timestampCommon.push(
            buffer.getNextSample(ST_U32, bi) +
              precedingTimestamp +
              Math.pow(2, bi) -
              1
          )
        } else {
          timestampCommon.push(precedingTimestamp)
        }
      }
    } else {
      timestampCommon.push(buffer.getNextSample(ST_U32))
    }
    lastTimestamp = timestampCommon[i]
  }
  return {
    timestampCommon: timestampCommon,
    lastTimestamp: lastTimestamp
  }
}

/**
 * Complete current measure from the preceding one
 */
function completeCurrentMeasure(buffer, precedingValue, codingType, resol, bi) {
  var currentValue = buffer.getNextSample(ST_U16, bi)
  if (codingType === 0) {
    // ADLC
    return computeAdlcValue(currentValue, resol, precedingValue, bi)
  }
  if (codingType === 1) {
    // Positive
    return (currentValue + Math.pow(2, bi) - 1) * resol + precedingValue
  }
  // Negative
  return precedingValue - (currentValue + (Math.pow(2, bi) - 1)) * resol
}

/**
 * Return current value in ADLC case
 */
function computeAdlcValue(currentValue, resol, precedingValue, bi) {
  if (currentValue >= Math.pow(2, bi - 1)) {
    return currentValue * resol + precedingValue
  }
  return (currentValue + 1 - Math.pow(2, bi)) * resol + precedingValue
}

/**
 * Uncompress data in case of separate timestamp
 */
function handleSeparateTimestamp(
  out,
  buffer,
  argList,
  last_timestamp,
  flag,
  tagsz
) {
  var tag = {}
  for (var i = 0; i < flag.nb_of_type_measure; i++) {
    tag.lbl = buffer.getNextSample(ST_U8, tagsz)
    var sampleIndex = findIndexFromArgList(argList, tag)
    var compressSampleNb = buffer.getNextSample(ST_U8, 8)
    if (compressSampleNb) {
      var timestampCoding = buffer.getNextSample(ST_U8, 2)
      for (var j = 0; j < compressSampleNb; j++) {
        var precedingRelativeTimestamp =
          out.series[sampleIndex].uncompressSamples[
            out.series[sampleIndex].uncompressSamples.length - 1
          ].data_relative_timestamp
        var currentMeasure = {
          data_relative_timestamp: 0,
          data: {}
        }
        var bi = buffer.getNextBifromHi(timestampCoding)
        currentMeasure.data_relative_timestamp = computeTimestampFromBi(
          buffer,
          precedingRelativeTimestamp,
          bi
        )
        if (currentMeasure.data_relative_timestamp > last_timestamp) {
          last_timestamp = currentMeasure.data_relative_timestamp
        }
        bi = buffer.getNextBifromHi(out.series[sampleIndex].codingTable)
        if (bi <= BR_HUFF_MAX_INDEX_TABLE) {
          var precedingValue =
            out.series[sampleIndex].uncompressSamples[
              out.series[sampleIndex].uncompressSamples.length - 1
            ].data.value
          if (bi > 0) {
            currentMeasure.data.value = completeCurrentMeasure(
              buffer,
              precedingValue,
              out.series[sampleIndex].codingType,
              argList[sampleIndex].resol,
              bi
            )
          } else {
            // bi <= 0
            currentMeasure.data.value = precedingValue
          }
        } else {
          // bi > BR_HUFF_MAX_INDEX_TABLE
          currentMeasure.data.value = buffer.getNextSample(
            argList[sampleIndex].sampletype
          )
        }
        out.series[sampleIndex].uncompressSamples.push(currentMeasure)
      }
    }
  }
  return last_timestamp
}

/**
 * Translate brUncompress output data to expected structure
 */
function adaptToExpectedFormat(out, argList, batchAbsoluteTimestamp) {
  var returnedGlobalObject = {
    //batch_counter: out.batch_counter,
    //batch_relative_timestamp: out.batch_relative_timestamp
  }
  if (batchAbsoluteTimestamp) {
    returnedGlobalObject.b_ts = batchAbsoluteTimestamp
  }
  returnedGlobalObject.datas = out.series.reduce(function(
    acc,
    current,
    index
  ) {
    return acc.concat(
      current.uncompressSamples.map(function(item) {
        var returned = {
          //data_relative_timestamp: item.data_relative_timestamp,
          data: {
            value: argList[index].divide
              ? item.data.value / argList[index].divide
              : item.data.value,

          }
        }
        if (argList[index].lblname) {
          returned.data.label = argList[index].lblname
        }
        if (batchAbsoluteTimestamp) {
          returned.date = computeDataAbsoluteTimestamp(
            batchAbsoluteTimestamp,
            out.batch_relative_timestamp,
            item.data_relative_timestamp
          )
        }
        return returned
      })
    )
  },
  [])
  return returnedGlobalObject
}

/**
 * Compute data absolute timestamp from batch absolute timestamp (bat), batch
 * relative timestamp (brt) and data relative timestamp (drt)
 */
function computeDataAbsoluteTimestamp(bat, brt, drt) {
  return new Date(new Date(bat) - (brt - drt) * 1000).toISOString()
}

try {
  module.exports = brUncompress
} catch (e) {
  // when called from nashorn,  module.exports is unavailable…
}


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
  } 
  else significand = (significand | (1 << 23)) / (1 << 23);

  return sign * significand * Math.pow(2, exponent);
}


function Decoder(bytes, port) {

  // Decode an uplink message from a buffer
  // (array) of bytes to an object of fields.
  var decoded = {};

  var decodedBatch = {};

  var lora = {};

  // decoded.lora.port  = port;
    
  // Get raw payload
  var bytes_len_ = bytes.length;
  var temp_hex_str = ""

  lora.payload  = "";




  for( var j = 0; j < bytes_len_; j++ ){
    temp_hex_str   = bytes[j].toString( 16 ).toUpperCase( );
    if( temp_hex_str.length == 1 ){
      temp_hex_str = "0" + temp_hex_str;
    }
    lora.payload += temp_hex_str;
    }

    var date = new Date();
    var lDate = date.toISOString();
    
    if (port === 125){
        //batch
        decodedBatch = !(bytes[0] & 0x01);
    
        //trame standard
        if (decodedBatch === false){

            //decoded.zclheader = {};
            //decoded.zclheader.report =  "standard";
            attributID = -1;
            cmdID = -1;
            clusterdID = -1;
            //endpoint
            //decoded.zclheader.endpoint = ((bytes[0]&0xE0)>>5) | ((bytes[0]&0x06)<<2);
            //command ID
            cmdID =  bytes[1];
						//decoded.zclheader.cmdID = decimalToHex(cmdID,2);
            //Cluster ID
            clusterdID = bytes[2]*256 + bytes[3];
						//decoded.zclheader.clusterdID = decimalToHex(clusterdID,4);
            
        
            // decode report and read atrtribut response
            if((cmdID === 0x0a)|(cmdID === 0x8a)|(cmdID === 0x01)){

                stdData = {};
                var tab=[];

                //Attribut ID
                attributID = bytes[4]*256 + bytes[5];
								//decoded.zclheader.attributID = decimalToHex(attributID,4);

                if (cmdID === 0x8a) {
                    decoded.alarm = 1;
                }
                else {
                    decoded.alarm = 0;
                }
                    
                //data index start
                if ((cmdID === 0x0a) | (cmdID === 0x8a)) index = 7;
                // if (cmdID === 0x01) {index = 8; decoded.zclheader.status = bytes[6];}


                //binary input counter
                if (  (clusterdID === 0x000f ) & (attributID === 0x0402)) {
                    //stdData.label = "counter";
                    //stdData.value = (bytes[index]*256*256*256+bytes[index+1]*256*256+bytes[index+2]*256+bytes[index+3]); 
                    //stdData.date = lDate;
                    stdData["counter"] = (bytes[index]*256*256*256+bytes[index+1]*256*256+bytes[index+2]*256+bytes[index+3]); 
										stdData["offset_milliseconds"] = 0.0;
										tab.push(stdData);
                };
            
                // binary input present value
                if (  (clusterdID === 0x000f ) & (attributID === 0x0055)) {
                    // if (decoded.zclheader.endpoint < 3){
                    //   stdData.label = "Index"+(decoded.zclheader.endpoint+1) ;  
                    // }
        
                    // if ((decoded.zclheader.endpoint >= 3)&&(decoded.zclheader.endpoint < 6)){
                    //   stdData.label = "State"+(decoded.zclheader.endpoint-2) ;
                    // }
                    //stdData.label = "counter";
                    //stdData.value = bytes[index]; 
                    //stdData.date = lDate;
										stdData[label] = bytes[index]; 
                    tab.push(stdData);
                };


                // lorawan message type
                if (  (clusterdID === 0x8004 ) & (attributID === 0x0000)) {
                    if (bytes[index] === 1)
                        stdData.message_type = "confirmed";
                    if (bytes[index] === 0)
                        stdData.message_type = "unconfirmed";
                }
                    
                // lorawan retry
                if (  (clusterdID === 0x8004 ) & (attributID === 0x0001)) {
                    stdData.nb_retry= bytes[index] ;
                }
                    
                // lorawan reassociation
                if (  (clusterdID === 0x8004 ) & (attributID === 0x0002)) {
                    stdData.period_in_minutes = bytes[index+1] *256+bytes[index+2];
                    stdData.nb_err_frames = bytes[index+3] *256+bytes[index+4];
                }

                // configuration node power desc

                if (   (clusterdID === 0x0050 ) & (attributID === 0x0006)) {
                    index2 = index + 3;
                    if ((bytes[index+2] &0x01) === 0x01) {
                        tab.push({battery_volt:(bytes[index2]*256+bytes[index2+1])/1000}) ;
                        index2=index2+2;
                    }
                    if ((bytes[index+2] &0x04) === 0x04) {
                        tab.push({battery_volt:(bytes[index2]*256+bytes[index2+1])/1000}) ;
                        index2=index2+2;
                    }
                    //if ((bytes[index+2] &0x02) === 0x02) {decoded.data.rechargeable_battery_voltage = (bytes[index2]*256+bytes[index2+1])/1000;index2=index2+2;}
                    //if ((bytes[index+2] &0x08) === 0x08) {decoded.data.solar_harvesting_voltage = (bytes[index2]*256+bytes[index2+1])/1000;index2=index2+2;}
                    //if ((bytes[index+2] &0x10) === 0x10) {decoded.data.tic_harvesting_voltage = (bytes[index2]*256+bytes[index2+1])/1000;index2=index2+2;}
                }
                decoded.data = tab;
            }  
        }

        else{

            var decoded = {};
            brData = (brUncompress(1,[{taglbl: 0,resol: 1, sampletype: 10,lblname: "counter", divide: 1},{ taglbl: 1, resol: 100, sampletype: 6,lblname: "battery_volt", divide: 1000}], lora.payload, lDate))

            var data_length = brData["datas"].length;
            var tab=[];
            for (var i = 0; i < data_length; i++) {
                var label = brData["datas"][i]["data"]["label"];
                var value = brData["datas"][i]["data"]["value"];
                var date = brData["datas"][i]["date"];
								var dateObj = new Date(date);
								var batchDateObj = new Date(lDate);
								var offset_milliseconds = batchDateObj - dateObj;
                
								var obj = {
                    offset_milliseconds: offset_milliseconds
                };
                obj[label] = value;
            
                tab.push(obj);
            }

            decoded.data = tab;

            //decoded.zclheader = {};
            //decoded.zclheader.report = "batch";
        }
    }
  return decoded.data;
}


function decodeUplink(input) {
 
  return {
    data : Decoder(input.bytes, input.fPort),
    
    warnings: [],
    errors: []
  };
}
```
{% endraw %}