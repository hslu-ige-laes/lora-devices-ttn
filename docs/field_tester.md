---
layout: default
title: Field Tester
nav_order: 5
has_children: false
permalink: /docs/field_testing_guideline
---

<img src="https://raw.githubusercontent.com/hslu-ige-laes/lora-devices-ttn/master/docs/rak10701-p_01.png" width="128" align="right" class="inline"/>

# LoRaWAN Field Testing Guideline
{: .no_toc }
This guide helps you **test LoRaWAN coverage** and **decide when to install a gateway**. Perfect for beginners testing in and around buildings, with any LoRaWAN tester and either The Things Network (TTN) or your own LTE LoRaWAN gateway.

---
## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## What You Need
- A LoRaWAN connection tester (e.g., RAK10701)
- LoRaWAN gateway(s) (public TTN gateways, or your own LTE gateways)

---

## How to Test Coverage

1. Charge your tester.
1. Mount the antenna on your tester.
1. Switch on the tester by pressing the button on the right side for at least five seconds.
   <img src="https://raw.githubusercontent.com/hslu-ige-laes/lora-devices-ttn/master/docs/rak10701-p_02.png" width="128" align="left" class="inline"/>
1. Take your tester to the first test location (e.g., office, basement, outdoors)
   > **Note:** If you are indoors, there will be no reception of the GPS signal. The latitude and longitude data will be empty.
1. Send a test uplink/join:
    - On most testers, this is a button press or auto-sends at intervals.
    - Watch for join success (for OTAA) and uplink send.
1. Observe and record the results:
    - RSSI (signal strength)
    - SNR (signal clarity)
    - Gateway count (number of gateways receiving your signal)
    - Spreading Factor (SF)
    - Packet loss / failed sends

    > **Important:** Note your location and results.

1. Repeat at all locations where you need coverage: every floor, corner, room, or outdoor spot.

1. If done, power off the device by pressing the button on the right side for at least five seconds.

---

## How to Interpret the Results

| Parameter | What’s Good? | What’s Bad? | Notes |
|-----------|--------------|-------------|-------|
| **RSSI**  | 0 to -90 dBm | below -110 dBm | Stronger is better; above -110 is reliable |
| **SNR**   | >+5 dB       | <0 dB       | Higher means clearer signal |
| **Gateway Count** | ≥2   | 0–1         | More gateways = better reliability |
| **Spreading Factor** | 7–9 | 11–12      | Low SF = good coverage; SF12 = weak signal |
| **Join Success** | Yes   | No          | Failed joins = no coverage or wrong keys |

### Quick Rules
- **Good Coverage:** RSSI > -100 dBm, SNR > 0 dB, GW ≥2, SF ≤9
- **Weak Coverage:** RSSI < -110 dBm, SNR < 0 dB, GW 0–1, SF ≥11
- **No Coverage:** No join or uplink; nothing in console

---

## When to Install (or Move) a Gateway

### Install a new gateway if:
- **Many locations show weak or no coverage:** (RSSI < -110 dBm, SNR < 0 dB, SF12, join failures)
- **Important areas (e.g., basement, far office) get 0 packets received.**
- **Only one gateway receives your tester in key spots:** (GW count = 1 or 0)
- **You have high packet loss or messages don’t reach the network.**
- **You want reliable indoor coverage across large or dense buildings.**

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

## Sample Test Table

| Location    | RSSI    | SNR   | GW Count | SF  | Join | Notes         |
|-------------|---------|-------|----------|-----|------|---------------|
| Entrance    | -75 dBm | +7 dB | 3        | 7   | Yes  | Great         |
| Basement    | -115 dBm| -3 dB | 0        | —   | No   | Needs gateway |
| Hallway 2F  | -105 dBm| +1 dB | 1        | 11  | Yes  | Marginal      |
| Parking Lot | -95 dBm | +4 dB | 2        | 9   | Yes  | Good          |

---

## Further Resources

- [The Things Network Mapper](https://ttnmapper.org/heatmap/) – for outdoor mapping coverage
- [LoRaWAN Knowledge Base](https://www.thethingsnetwork.org/docs/)
- [LoRaWAN Tester Quick Start Guides](https://docs.rakwireless.com/Product-Categories/WisBlock/RAK10701/Quickstart/)

---