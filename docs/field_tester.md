---
layout: default
title: Field Testing Guideline
nav_order: 5
has_children: false
permalink: /docs/field_testing_guideline
---

<img src="https://raw.githubusercontent.com/hslu-ige-laes/lora-devices-ttn/master/docs/rak10701-p_01.png" width="128" align="right" class="inline"/>

# LoRaWAN Field Testing Guideline
{: .no_toc }
This guide helps you test LoRaWAN coverage and decide when to install a gateway



---
## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## What You Need
- A LoRaWAN connection tester (e.g., RAK10701)
- LoRaWAN gateway(s) - public TTN gateways, or your own LTE gateways

---

## Typical range of transmission
The typical range for LoRaWAN devices is a few hundred meters to a few kilometers in urban areas and several kilometers in rural or open environments.
It is as well possible to place devices in buildings and have the next gateway somewhere outside.

However, the actual range can vary depending on several factors
- **Distance to the Gateway**<br>
  The farther the sensor device is from the gateway, the weaker the signal, impacting communication reliability.
- **Obstacles and Environment**<br>
  Physical obstacles like buildings, walls, windows and trees can weaken or block the signal, affecting signal strength and range.
- **Gateway Density**<br>
  More gateways nearby improve coverage and increase the chances of successful communication. That has as well an effect on battery life. The better the reception, the faster the device can transmit, the shorter its airtime.
- **Transmit Power of the Sensor Device**<br>
  Higher transmit power allows for longer range but consumes more energy.
- **Environmental Factors**<br>
  Weather, humidity, and atmospheric conditions can influence signal strength.
- **Interference**<br>
  Electronic noise or interference from other devices can impact reception and as well battery life.
- **Antenna Quality**<br>
  The quality of antennas on both the sensor device and gateway affects range and reception quality.
- **Sensor Device Orientation**<br>
  Each antenna has a designed orientation. Refer to the sensor device datasheets to see how you orient best the devices.

---

## How to Test Coverage

1. Charge your tester
2. Mount the antenna on your tester
3. Switch on the tester by pressing the button on the right side for at least five seconds
   <img src="https://raw.githubusercontent.com/hslu-ige-laes/lora-devices-ttn/master/docs/rak10701-p_02.png" width="128" align="left">
4. Take your tester to the first test location (e.g., office, basement, outdoors)
   > **Note:** If you are indoors, there will be no reception of the GPS signal. The latitude and longitude data will be empty.
5. Send a test uplink/join:
    - On most testers, this is a button press or auto-sends at intervals.
    - Watch for join success (for OTAA) and uplink send.
6. Observe and record the results:
    - RSSI (signal strength)
    - SNR (signal clarity)
    - Gateway count (number of gateways receiving your signal)
    - Spreading Factor (SF)
    - Packet loss / failed sends

    > **Important:** Note your location and results, see example table below

7. Repeat at all locations where you need coverage: every floor, corner, room, or outdoor spot.

8. If done, power off the device by pressing the button on the right side for at least five seconds.

| Location    | RSSI    | SNR   | GW Count | SF  | Join | Notes         |
|-------------|---------|-------|----------|-----|------|---------------|
| Entrance    | -75 dBm | +7 dB | 3        | 7   | Yes  | Great         |
| Basement    | -115 dBm| -3 dB | 0        | —   | No   | Needs gateway |
| Hallway 2F  | -105 dBm| +1 dB | 1        | 11  | Yes  | Marginal      |
| Parking Lot | -95 dBm | +4 dB | 2        | 9   | Yes  | Good          |

---

## How to Interpret the Results

| Parameter | What’s Good? | What’s Bad? | Notes |
|-----------|--------------|-------------|-------|
| **RSSI**  | 0 to -90 dBm | below -120 dBm | Stronger is better; above -110 is reliable, below -130 dBm are not ok |
| **SNR**   | >+5 dB       | <0 dB       | Higher means clearer signal |
| **Gateway Count** | ≥2   | 0–1         | More gateways = better reliability |
| **Spreading Factor (SF)** | 7–9 | 11–12      | Low SF = good coverage; SF12 = weak signal |
| **Join Success** | Yes   | No          | Failed joins = no coverage or wrong keys |

### Quick Rules
- **Good Coverage:** RSSI > -100 dBm, SNR > 0 dB, GW ≥2, SF ≤9
- **Weak Coverage:** RSSI < -110 dBm, SNR < 0 dB, GW 0–1, SF ≥11
- **No Coverage:** No join or uplink; nothing in console

---

### RSSI and SNR

In the LoRaWAN network, the Received Signal Strength Indicator (RSSI) and the Signal-to-Noise Ratio (SNR) are important metrics for evaluating the communication quality between LoRa devices and gateways.

#### **RSSI** (Received Signal Strength Indicator)

RSSI measures the strength of the received signal in decibels relative to 1 milliwatt (dBm).
The values for RSSI in the LoRaWAN can typically range from around -120 dBm to -40 dBm. A value of -120 dBm indicates a very weak signal that is close to the reception limit, while a value of -40 dBm indicates a very strong signal.


#### **SNR** (Signal-to-Noise Ratio)

SNR is a measure of how much stronger the signal is than the background noise. It is measured in decibels (dB).
The SNR values in the LoRaWAN can range from around -20 dB to +10 dB.
- A negative SNR means that the noise is stronger than the signal, which typically occurs at very long distances or in poor environmental conditions.
- A positive SNR indicates that the signal is stronger than the noise, which is necessary for effective communication.

#### **What are good values?**

**To assess the quality, the two values must be considered together!**

- The radio link can be described as **GOOD** if RSSI > -115dB and SNR > -7dB
- The radio link is **POOR** (range limit) if RSSI <= -120 dB or SNR <= -13dB

If RSSI is good (> -115dB), but SNR is poor (<= -13dB), this means that the environment is very noisy

The SNR must be checked over several days to be sure that the radio link is stable enough to receive all messages.

If RSSI is poor (<=-120dB) but SNR is good (> -7dB), this means that the device is probably far away from the gateway.


---

## When to Install (or move) a Gateway

### Install a new gateway if:
- Many locations show weak or no coverage: (RSSI < -110 dBm, SNR < 0 dB, SF12, join failures)
- Important areas (e.g., basement, far office) get 0 packets received.
- Only one gateway receives your tester in key spots: (GW count = 1 or 0)
- You have high packet loss or messages don’t reach the network.
- You want reliable indoor coverage across large or dense buildings.

### How to Position the Gateway
- Place the gateway as **high** and **central** as possible.
- Avoid metal enclosures or dense walls.
- Use an external antenna if possible for more range.

### After Gateway Install
- Repeat tests in previous weak spots to verify coverage improved.
- Adjust antenna or gateway placement if needed.

---

## Tips for reliable testing

- Test at different times of day (buildings can affect signal depending on occupancy).
- Walk slowly and wait for test results in each location.
- Note obstacles (concrete, metal) that may block the signal.

---

## Further Resources

- <a href="https://ttnmapper.org/heatmap/" target="_blank">The Things Network Mapper – for outdoor mapping coverage</a>
- <a href="https://www.thethingsnetwork.org/docs/" target="_blank">LoRaWAN Knowledge Base</a>  
- <a href="https://docs.rakwireless.com/Product-Categories/WisBlock/RAK10701/Quickstart/" target="_blank">LoRaWAN Tester Quick Start Guides</a>

---