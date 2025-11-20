---
layout: default
title: Keyence FD-Q + LoRaWAN Logger
parent: Sensors
---

<img src="https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/keyence-fd-q-logger-pic.png" width="250" align="right">

# Keyence FD-Q + LoRaWAN Logger
{: .no_toc }

- Manufacturer ultrasonic sensor: <a href="https://www.keyence.com/" target="_blank">Keyence Corporation</a>
- Manufacturer Logger: HSLU T&A IGE
- Product: <a href="https://www.keyence.com/products/process/flow/fd-q/" target="_blank">FD-Q Series</a>

The Keyence FD-Q ultrasonic flow sensor can get extended with a self-made LoRaWAN-capable ESP-based module.
The module measures flow from the Keyence sensor via the 4-20mA signal and captures supply/return temperatures with PT100 sensors for thermal power and energy calculation.

---

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Specifications

- **Indoor device**
- **Built-in sensors:**
  - Water Flow via Keyence [l/min] via 4-20mA
  - 2× Temperature PT100 4-Wire (Supply/Return) [°C]
- **Calculated values:**
  - Cumulative Volume [m³]
  - Thermal Power [kW]
  - Cumulative Energy [kWh]
- **Pipe diameter:** Keyence model dependent
- **Power Supply:** 24 VDC (Keyence, LoRa-Module gets 5V DC via a step down converter)
- **LoRaWAN version:** 1.0.3
- **LoRaWAN device class:** C
- **Display:** 200×200 px E-Paper

---

## System Architecture

```
┌─────────────────┐         ┌──────────────────────┐
│   Keyence FD-Q  │  4-20mA │   LoRaWAN Logger     │   LoRaWAN
│   Flow Sensor   │────────▶│   (M5Stack)          │──────────▶ TTN
│   (Clamp-On)    │         │   - Flow Reading     │
└─────────────────┘         │   - 2× PT100 Temp    │
                            │   - Energy Calc      │
┌─────────────────┐         │   - Display          │
│ PT100 Supply    │────────▶│   - NVS Storage      │
└─────────────────┘         └──────────────────────┘
┌─────────────────┐         
│ PT100 Return    │────────▶
└─────────────────┘         
```

**Key Features:**
- Non-invasive flow measurement (ultrasonic clamp-on)
- Precise 4-wire PT100 temperature sensors
- Persistent counters (survive power loss)
- Local E-Paper display
- Remote configuration via LoRaWAN downlinks
- 2-point calibration for 4-20mA signal

---

## Documents

- [Keyence FD-Q product page online](https://www.keyence.com/products/process/flow/fd-q/)
- [Overview German](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/keyence-fd-q-logger-produkteübersicht.pdf)
- [User Manual German](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/sensors/keyence-fd-q-logger-bedienungsanleitung.pdf)

---

## Ordering Info

### Complete Kit

| Component | Type | Qty | Price (approx.) |
|-----------|------|-----|-----------------|
| Keyence FD-Q | FD-Q10C (DN20-40) | 1 | model dependent, not included|
| M5Stack Core Ink | K048 | 1 | CHF 45.- |
| RAK3172 Module | LoRaWAN Breakout | 1 | CHF 25.- |
| 4-20mA Unit | M5Stack AIN4-20mA | 1 | CHF 15.- |
| MAX31865 | PT100 Amplifier | 2 | CHF 30.- |
| PT100 Sensors | 4-wire, Class A | 2 | CHF 40.- |
| Enclosure | DIN-rail mountable | 1 | CHF 25.- |
| Cables | Various | - | CHF 20.- |
| **TOTAL** | | | **~CHF 175.-** |

---

## Calibration

### 2-Point Calibration (4-20mA Signal)

**Why needed?**
- Compensates tolerances in current loop

**Procedure:**

1. **Set Keyence to Test Mode:**
   - Keyence Menu: Enable `Test Mode`
   - Set output to `4.00 mA` (Min)

2. **Start Calibration on Logger:**
   - Hold BTN_MENU for 2 seconds → Settings
   - Navigate to `Calibration`
   - Press BTN_MENU

3. **Capture 4mA Point:**
   - Wait for stable reading (~4.0 mA)
   - Press BTN_MENU → value saved

4. **Capture 20mA Point:**
   - Set Keyence output to `20.00 mA` (Max)
   - Wait for stable reading (~20.0 mA)
   - Press BTN_MENU → value saved

5. **Done:**
   - Display shows "DONE!" with calibration values
   - Values stored in NVS (permanent)
   - Disable Keyence Test Mode

---

## Optional Settings

### Change TX Interval (Transmission Frequency)

**Local (via Display):**
1. Settings → `TX Interval`
2. Set value (1-240 minutes)
3. Save

**Remote (LoRaWAN Downlink):**
```
Port: 10
Payload: 01 0F  (15 minutes)
         01 1E  (30 minutes)
         01 3C  (60 minutes)
```

### Set Energy Mode (Heating/Cooling)

**Local:**
1. Settings → `Energy Mode`
2. Select `HEAT` or `COOL`
3. Save

**Effect:**
- **HEAT:** ΔT = T_Supply - T_Return (positive for heating)
- **COOL:** ΔT = T_Return - T_Supply (positive for cooling)

⚠️ Changing mode resets volume and energy counters!

### Reset Counters

**Local:**
- Settings → `Reset Counters`

**Remote:**
```
Port: 10
Payload: 02
```

---

## Payload Decoder

```javascript
function decodeUplink(input) {
  var bytes = input.bytes;
  
  if (bytes.length !== 17) {
    return { errors: ["Invalid payload length"] };
  }
  
  // Type and Temperature Flow (10-bit)
  var type = (bytes[0] >> 4) & 0x0F;
  var t_fl_raw = ((bytes[0] & 0x0F) << 6) | ((bytes[1] >> 2) & 0x3F);
  
  // Temperature Return (10-bit)
  var t_rt_raw = ((bytes[1] & 0x03) << 8) | bytes[2];
  
  // Flow (l/min)
  var flow_raw = (bytes[3] << 8) | bytes[4];
  var flow = flow_raw / 10.0;
  
  // Volume (Liters to m³)
  var volume_L = (bytes[5] << 24) | (bytes[6] << 16) | (bytes[7] << 8) | bytes[8];
  var volume_m3 = volume_L / 1000.0;
  
  // Power (Watts to kW)
  var power_W = (bytes[9] << 16) | (bytes[10] << 8) | bytes[11];
  var power_kW = power_W / 1000.0;
  
  // Energy (Wh × 10 to kWh)
  var energy_raw = (bytes[12] << 24) | (bytes[13] << 16) | (bytes[14] << 8) | bytes[15];
  var energy_Wh = energy_raw / 10.0;
  var energy_kWh = energy_Wh / 1000.0;
  
  // Flags
  var flags = bytes[16];
  var energy_mode = (flags & 0x01) ? "Cooling" : "Heating";
  var sensor_loc = (flags & 0x02) ? "Return" : "Flow";
  
  // Temperature conversion (1023 = error)
  var t_fl = (t_fl_raw === 1023) ? null : t_fl_raw / 10.0;
  var t_rt = (t_rt_raw === 1023) ? null : t_rt_raw / 10.0;
  
  return {
    data: {
      type: type,
      flow_lmin: (flow_raw === 65535) ? null : flow,
      volume_m3: (volume_L === 0xFFFFFFFF) ? null : volume_m3,
      temp_supply_C: t_fl,
      temp_return_C: t_rt,
      power_kW: (power_W === 0xFFFFFF) ? null : power_kW,
      energy_kWh: (energy_raw === 0xFFFFFFFF) ? null : energy_kWh,
      energy_mode: energy_mode,
      sensor_location: sensor_loc
    }
  };
}
```

### Payload Structure (17 Bytes)

| Byte | Description | Unit | Format |
|------|-------------|------|--------|
| 0 | Type + Temp Flow [9:6] | - | uint8 |
| 1 | Temp Flow [5:0] + Temp Return [9:8] | - | uint8 |
| 2 | Temp Return [7:0] | - | uint8 |
| 3-4 | Flow × 10 | l/min | uint16 BE |
| 5-8 | Volume | Liters | uint32 BE |
| 9-11 | Power | Watts | uint24 BE |
| 12-15 | Energy × 10 | Wh | uint32 BE |
| 16 | Flags (Mode/Location) | - | uint8 |

**Invalid Value Indicators:**
- Temperature: 1023 (0x3FF)
- Flow: 65535 (0xFFFF)
- Volume: 4294967295 (0xFFFFFFFF)
- Power: 16777215 (0xFFFFFF)
- Energy: 4294967295 (0xFFFFFFFF)

---

## Downlink Commands (Port 10)

### CMD 0x01: Set TX Interval
```
Byte 0: 0x01
Byte 1: Interval in minutes (1-240)

Examples:
  01 05  → 5 minutes
  01 0F  → 15 minutes
  01 1E  → 30 minutes

Response: 81 [Interval]
```

### CMD 0x02: Reset Counters
```
Byte 0: 0x02

Response: 82
Note: Sends uplink after reset
```

### CMD 0x05: Query Configuration
```
Byte 0: 0x05

Response (5 bytes):
  Byte 0: 0x85
  Byte 1: TX Interval (min)
  Byte 2: Flags (Energy Mode, Sensor Location)
  Byte 3: Flow Max / 10
  Byte 4: Reserved (0x00)
```

---

## Troubleshooting

### "Join FAIL"
- Check LoRaWAN credentials (DevEUI/AppEUI/AppKey)
- Verify gateway coverage (RSSI > -120 dBm)
- Check antenna connection
- Auto-retry every 60 seconds

### "Sensor Err" on Display
- Check 4-20mA wiring (must be 3-22 mA range)
- Verify Keyence power supply
- Check PT100 connections (4-wire)
- Read Serial Monitor for details

### No Display
- Check 5V power supply (min. 1A)
- Verify PWR button is bridged
- Press any button to wake from standby

### Display Stays in Standby
- Press any button to wake
- Standby activates after 60s inactivity

---
