---
layout: default
title: Oxon - Buttonboard
parent: Sensors
---

<img src="https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/oxon-buttonboard.png" width="250" align="right">

# Oxon - Buttonboard
{: .no_toc }

- Manufacturer: <a href="https://oxon.ch/" target="_blank">Oxon</a>
- Product: <a href="https://www.oxobutton.ch/products/buttonboard-lorawan/" target="_blank">Buttonboard</a>

The Oxon Buttonboard is a flexible device with 6 capacitive touch sensor buttons which can be enabled individually and each comes with a RGB-LED ring each. 
Its layout can be easily adapted by printing a paper overlay with the required text and an individual layout.
If a button is pressed, the LEDs shimmer through the paper inlay and generates a fantastic user experience. 

---

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Specifications
- indoor device
- Price ca. CHF 99.- (30.07.2025, bulk prices upon request)
- Features
  - <b>6 capacitive touch sensor buttons</b>
  - <b>6 LED rings with each 8 ultrabright multicolor LEDs around the buttons</b
  - <b>Temperature</b>, ± 2 °C
  - <b>Accelerometer (x, y, z)</b>
  - <b>Vibration motor for touch feedback</b>
  - <b>Piezo speaker for touch feedback</b>
- LoRaWAN v1.0.3 Class A
- Power Supply: 1 lithium battery (CP405050), 3V, 2400 mAh
  - Expected life time: up to 4 years with 12 uplinks per day
- Size: 100 × 162 × 9 mm

---
## Documents

- [SpecSheet from oxonbutton.ch (30.07.2025)](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/oxon-buttonboard_specSheet.pdf)
- [Documentation from oxonbutton.ch (30.07.2025)](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/oxon-buttonboard_documentation.pdf)
- [Paper Inlay Template for Visio](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/oxon-buttonboard_paperInlayTemplate.vsdx)

---

## Ordering Info

- [Ordering Link](https://shop.oxon.ch/articles/19?shop=IOT)

---

## Configuration

The device can either be configured via Bluetooth and an app called `Oxon Device Manager` mobile app or via LoRa downlink messages.

- To swith on/off the device, open the case and there you find an on/off switch
- To activate Bluetooth, open the case and press the BLE button

### Feature Set

- **Configurable Capacitive Touch Buttons:**  
  Individually enable or disable each button.

- **Feedback:**  
  - Audio signals for confirmations and errors  
  - Vibration (haptic feedback) on button presses

- **Customizable LED Rings:**  
  Each button features an 8-pixel multi-color LED ring with user-selectable colors.

- **Operation Modes:**  
  - **Mode 1:** All 6 buttons send an uplink message when pressed  
  - **Mode 2:** Buttons 1–5 can be selected/deselected; only button 6 sends an uplink  
  - **Mode 3:** Buttons 2–6 can be selected/deselected; only button 1 sends an uplink

- **Transmitting mode (can only be set via LoRa downlinks)**  
  Device either sends a telegram ...
	- ... at each button click (good if timestamp is of interest, e.g. for a survey where later on the result gets matched with a temperature sensor value)
	- ... periodically with summarized button presses (good for a survey where lots of people answer and the timestamt is not of interest)

### Example LoRa Downlink messages

Please refer to the payload description in the [Documentation ](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/oxon-buttonboard_documentation.pdf). Here some examples...

1. In the TTN Console on the device view, select the device and change to the tab `Messaging`, select `Downlink`
2. Change the `FPort to 1`
3. Copy/paste a payload from below
4. Press `Send`
5. In the `Live data` tab you should now see the scheduled telegram. The device only receives downlink data after a transmission. Therefore start a transmission by pressing a button or wait for the next regular transmission to happen.

- `B000050303010100600005`: ADR off, SF7, 3 Send Trials, 3 Uplink Trials, FPort 1, Confirmed messages, Heartbeat every 24 hours 24*15 = 96 -> 0x60, 5s minimum interval until next message
- `C0`: Triggers that device sends power settings states
- `C100000103`: Batch every 10 minutes, Power safe mode, Sending and Feedback are shown in LED

---

## Payload Decoder
- Now you can see the incoming telegrams in the tab Data, but their content, the payload, is cryptic...!<br>
- We need to tell the "The Things Network" where to find e.g. the temperature etc. in these cryptic numbers and letters. We can do that with configuring a "Payload Decoder Function".

1. [Log in](https://console.thethingsnetwork.org/applications) and open the `application`
2. Select the tab `Payload formatters > Uplink` and copy/paste the following code
3. Press `save payload functions`

- Now you should be able to see the decoded data of your device in the tab `Live data`.<br>

```javascript
function decodeUplink({ bytes }) {
  const messageId = bytes[0];
  let data = {};
  const warnings = [];
  const errors = [];

  switch (messageId) {
    case 0x12:
      data = { message: 'Settings saved' };
      break;
    case 0x31:
      data = buttonboardStateUpdate(bytes);
      break;
    case 0x32:
      data = buttonboardBatchUpdate(bytes);
      break;
    case 0x40:
      data = buttonboardPowerSaveConfigs(bytes);
      break;
    default:
      warnings.push('Unknown message ID: 0x' + toHexString(messageId));
      break;
  }

  return {
    data,
    warnings,
    errors,
  };
}

function toHexString(byte) {
  return ('0' + (byte & 0xff).toString(16).toUpperCase()).slice(-2);
}

function parseNonZeroButtonCountsFromBitmask(byte) {
  const result = {};
  if (byte & 0x01) result["count_abs@button1"] = 1;
  if (byte & 0x02) result["count_abs@button2"] = 1;
  if (byte & 0x04) result["count_abs@button3"] = 1;
  if (byte & 0x08) result["count_abs@button4"] = 1;
  if (byte & 0x10) result["count_abs@button5"] = 1;
  if (byte & 0x20) result["count_abs@button6"] = 1;
  return result;
}

function parseNonZeroButtonCountsFromValues(values) {
  const result = {};
  for (let i = 0; i < 6; i++) {
    const count = values[i];
    if (count > 0) {
      result[`count_abs@button${i + 1}`] = count;
    }
  }
  return result;
}

/* ID 0x31 - Single Button Press */
function buttonboardStateUpdate(bytes) {
  const buttonCounts = parseNonZeroButtonCountsFromBitmask(bytes[1]);

  const battery_perc_abs = bytes[6];
  const temperature_degrC_abs = bytes[7];

  return {
    ...buttonCounts,
    battery_perc_abs,
    temperature_degrC_abs,
  };
}

/* ID 0x32 - Summarized Counts */
function buttonboardBatchUpdate(bytes) {
  const values = [
    bytes[1] * 256 + bytes[2],
    bytes[3] * 256 + bytes[4],
    bytes[5] * 256 + bytes[6],
    bytes[7] * 256 + bytes[8],
    bytes[9] * 256 + bytes[10],
    bytes[11] * 256 + bytes[12],
  ];
  return parseNonZeroButtonCountsFromValues(values);
}

/* ID 0x40 - Config */
function buttonboardPowerSaveConfigs(bytes) {
  return {
    batchTime: bytes[1] * 256 + bytes[2] + ' sec',
    ledPowerSaveMode: !!bytes[3],
    showSendStatus: !!(bytes[4] & 0x01),
    showLoRaStatus: !!(bytes[4] & 0x02),
  };
}

```

