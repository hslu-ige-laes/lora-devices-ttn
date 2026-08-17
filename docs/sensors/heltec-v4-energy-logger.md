---
layout: default
title: Heltec V4 LoRaWAN Energy Logger
parent: Sensors
---

<img src="https://hslu-ige-laes.github.io/lora-devices-ttn/docs/sensors/heltec_universal_logger_01.png" width="250" align="right">

# Heltec V4 LoRaWAN Energy Logger
{: .no_toc }

- Manufacturer: <a href="https://www.hslu.ch/laes" target="_blank">HSLU T&A IGE LAES</a>
- MCU/LoRa Board: <a href="https://heltec.org/project/wifi-lora-32-v4/" target="_blank">Heltec WiFi LoRa 32 V4</a>

Universal LoRaWAN logger for flow, temperature and energy measurement.
Supports both analog sensors (4-20mA, PT100) and Modbus RTU (e.g. TUF-2000M).
Each measurement value can be sourced individually from analog or Modbus via a WiFi configuration portal.

---

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Specifications

- **Indoor device** (IP66 enclosure)
- **Built-in sensors:**
  - Water Flow [l/min] via 4-20mA (ADS1115 ADC)
  - 2x Temperature PT100 4-Wire (Supply/Return) [°C] (MAX31865)
- **Optional sensors:**
  - RS485/Modbus RTU (e.g. TUF-2000M ultrasonic flow meter)
- **Calculated values:**
  - Cumulative Volume [m³]
  - Thermal Power [kW]
  - Cumulative Energy [kWh]
- **Power Supply:** 7-27 VDC (via DD2712SA step-down to 5V)
- **LoRaWAN version:** 1.0.2 Rev. B
- **LoRaWAN device class:** C
- **LoRaWAN region:** EU868
- **Display:** OLED SSD1306 128x64 (built-in on Heltec board)
- **Configuration:** WiFi Access Point with web portal

---

## System Architecture

```
┌─────────────────┐         ┌──────────────────────────────┐
│ 4-20mA Flow     │  Analog │   Heltec WiFi LoRa 32 V4    │   LoRaWAN
│ Sensor          │────────▶│   (ESP32-S3 + SX1262 + OLED) │──────────▶ TTN
└─────────────────┘         │                              │
┌─────────────────┐         │   - ADS1115 ADC (4-20mA)    │
│ PT100 Supply    │──SPI───▶│   - 2x MAX31865 (PT100)     │
└─────────────────┘         │   - Energy Calculation       │
┌─────────────────┐         │   - OLED Display (4 pages)   │
│ PT100 Return    │──SPI───▶│   - NVS Persistent Counters  │
└─────────────────┘         │   - WiFi Config Portal       │
┌─────────────────┐         │   - RGB LED Status           │
│ Modbus Device   │  RS485  │                              │
│ (optional)      │────────▶│                              │
└─────────────────┘         └──────────────────────────────┘
```

**Key Features:**
- Per-value source selection (4-20mA/PT100 or Modbus) via WiFi portal
- Clamp-on sensor correction for surface-mounted PT100
- Persistent counters (survive power loss) with adaptive NVS strategy
- 4-page OLED display with status bar
- RGB LED status indicator (green=OK, red=error, blue=WiFi)
- Remote configuration via LoRaWAN downlinks
- 2-point calibration for 4-20mA signal
- FreeRTOS dual-core (UI on Core 0, main logic on Core 1)

---

## Documents

- <a href="https://gitlab.com/hslu-ige-laes/heltec-v4-lorawan-universal-logger" target="_blank">Source Code (GitLab)</a>

---

## Ordering Info

### Complete Kit

| Component | Description | Qty | Price (approx.) |
|-----------|-------------|-----|-----------------|
| Heltec WiFi LoRa 32 V4 | ESP32-S3 + SX1262 LoRa + OLED (incl. external antenna) | 1 | CHF 23.40 |
| MAX31865 Breakout | PT100 RTD-to-Digital, 4-Wire, VIN = 3.3V | 2 | CHF 5.40 |
| TTL-RS485 Auto | Modbus RTU (optional), VCC = 5V, Auto DE/RE | 1 | CHF 1.80 |
| GY-ADS1115 ADC | 16-Bit I2C ADC (Addr 0x48), VDD = 3.3V | 1 | CHF 0.85 |
| DD2712SA Step-Down | Buck Converter, 4.5-27V Input, 5V Output | 1 | CHF 1.10 |
| 150 Ohm Resistor 0.1% | 4-20mA shunt (0.60-3.00V) | 1 | - |
| PT100 4-Wire Sensors | Class A, surface mount (pipe clamp) | 2 | CHF 36.00 |
| PCB (PCBWay) | Custom PCB v0.1 | 1 | CHF 9.00 |
| Enclosure IP66 | 115x90x55mm | 1 | CHF 10.90 |
| Connectors, Cables | Screw terminals, cable glands, standoffs | - | CHF 17.10 |
| **TOTAL** | | | **~CHF 106.-** |

*Prices as of February 2026, partially without shipping. Flow sensor not included.*

---

## TTN Configuration

### Step 1 - Add end device in TTN

1. Log in to <a href="https://eu1.cloud.thethings.network/console/" target="_blank">The Things Network Console</a>
2. Go to **Applications** -> your application -> **End devices** -> **+ Register end device**
3. Select **Enter end device specifics manually**
4. Configure:
   - **Frequency plan:** `Europe 863-870 MHz (SF9 for RX2 - recommended)`
   - **LoRaWAN version:** `LoRaWAN Specification 1.0.3`
   - **Regional Parameters version:** `RP001 Regional Parameters 1.0.3 revision A`
5. Enter IDs:
   - **DevEUI:** Read from the device Serial Monitor or OLED LoRa page
   - **AppEUI / JoinEUI:** `1AE5CAFEBABEBEEF` (default, changeable via WiFi portal)
   - **AppKey:** `AD3461FE9687C3550F5E45A016AEDCEC` (default, changeable via WiFi portal)

### Step 2 - Add payload decoder

1. In TTN Console, go to your application -> **Payload formatters** -> **Uplink**
2. Select **Custom Javascript formatter**
3. Paste the following decoder:

```javascript
function decodeUplink(input) {
  const bytes = input.bytes;

  if (!bytes || bytes.length < 1) {
    return { errors: ["Payload too short"] };
  }

  const type = bytes[0];

  // ===== ACK PAYLOADS (0x81-0x85) =====
  if (type === 0x81) {
    if (bytes.length < 2) {
      return { errors: ["TX Override ACK: payload too short"] };
    }
    return {
      data: {
        ack_type: "tx_interval_override",
        tx_interval_min: bytes[1]
      }
    };
  }

  if (type === 0x82) {
    return {
      data: {
        ack_type: "counters_reset",
        status: "success"
      }
    };
  }

  if (type === 0x85) {
    if (bytes.length < 5) {
      return { errors: ["Config Query: payload too short"] };
    }
    const energyFlags = bytes[2];
    const energyMode = (energyFlags & 0x01) ? "cooling" : "heating";
    const sensorLocation = (energyFlags & 0x02) ? "return" : "flow";
    return {
      data: {
        ack_type: "config_query_response",
        tx_interval_min: bytes[1],
        energy_mode: energyMode,
        sensor_location: sensorLocation,
        flow_max_lpm: bytes[3] * 10
      }
    };
  }

  // ===== VERSION 3 SENSOR DATA (17 Bytes) =====
  if ((type >> 4) === 0x3) {
    if (bytes.length < 17) {
      return { errors: ["Sensor payload v3 too short"] };
    }

    const tempFlowHigh = bytes[0] & 0x0F;
    const tempFlowLow = (bytes[1] >> 2) & 0x3F;
    const tempReturnHigh = bytes[1] & 0x03;
    const tempReturnLow = bytes[2];

    const tFlowRaw = (tempFlowHigh << 6) | tempFlowLow;
    const tReturnRaw = (tempReturnHigh << 8) | tempReturnLow;

    const temperature_degrC_abs_flow = (tFlowRaw === 1023) ? null : tFlowRaw / 10.0;
    const temperature_degrC_abs_return = (tReturnRaw === 1023) ? null : tReturnRaw / 10.0;

    const flowRaw = (bytes[3] << 8) | bytes[4];
    const flow_l_min = (flowRaw === 65535) ? null : flowRaw / 10.0;
    const volumeFlow_m3perh_abs = (flow_l_min === null) ? null : flow_l_min * 60.0 / 1000.0;

    const vol_m3_x1000 =
      ((bytes[5] << 24) >>> 0) |
      ((bytes[6] << 16) >>> 0) |
      ((bytes[7] << 8)  >>> 0) |
      ((bytes[8] << 0)  >>> 0);
    const volume_m3_inc = (vol_m3_x1000 === 0xFFFFFFFF) ? null : vol_m3_x1000 / 1000.0;

    const powerRaw =
      ((bytes[9] << 16) >>> 0) |
      ((bytes[10] << 8)  >>> 0) |
      ((bytes[11] << 0)  >>> 0);
    const power_kW_abs = (powerRaw === 0xFFFFFF) ? null : powerRaw / 1000.0;

    const energyRaw =
      ((bytes[12] << 24) >>> 0) |
      ((bytes[13] << 16) >>> 0) |
      ((bytes[14] << 8)  >>> 0) |
      ((bytes[15] << 0)  >>> 0);
    const energy_kWh_inc = (energyRaw === 0xFFFFFFFF) ? null : energyRaw / 10.0;

    const flags = bytes[16];
    const energyMode = (flags & 0x01) ? "cooling" : "heating";
    const sensorLocation = (flags & 0x02) ? "return" : "flow";

    const powerKey = `power_kW_abs@${energyMode}`;
    const energyKey = `energy_kWh_inc@${energyMode}`;

    const result = {
      data: {
        payload_type: "sensor_data_v3",
        volumeFlow_m3perh_abs: volumeFlow_m3perh_abs,
        volume_m3_inc: volume_m3_inc,
        "temperature_degrC_abs@flow": temperature_degrC_abs_flow,
        "temperature_degrC_abs@return": temperature_degrC_abs_return,
        sensor_location: sensorLocation
      }
    };

    result.data[powerKey] = power_kW_abs;
    result.data[energyKey] = energy_kWh_inc;

    return result;
  }

  // ===== LEGACY VERSION 2 (18 Bytes) =====
  if (type === 0x02) {
    if (bytes.length < 7) {
      return { errors: ["Sensor payload too short"] };
    }

    if (bytes.length === 18) {
      let flowRaw = (bytes[1] << 8) | bytes[2];
      if (flowRaw & 0x8000) {
        flowRaw = flowRaw - 0x10000;
      }
      const flow_l_min = flowRaw / 10.0;
      const volumeFlow_m3perh_abs = flow_l_min * 60.0 / 1000.0;

      const vol_m3_x1000 =
        ((bytes[3] << 24) >>> 0) |
        ((bytes[4] << 16) >>> 0) |
        ((bytes[5] << 8)  >>> 0) |
        ((bytes[6] << 0)  >>> 0);
      const volume_m3_inc = vol_m3_x1000 / 1000.0;

      let tFlowRaw = (bytes[7] << 8) | bytes[8];
      if (tFlowRaw & 0x8000) {
        tFlowRaw = tFlowRaw - 0x10000;
      }
      const temperature_degrC_abs_flow = tFlowRaw === -32768 ? null : tFlowRaw / 10.0;

      let tReturnRaw = (bytes[9] << 8) | bytes[10];
      if (tReturnRaw & 0x8000) {
        tReturnRaw = tReturnRaw - 0x10000;
      }
      const temperature_degrC_abs_return = tReturnRaw === -32768 ? null : tReturnRaw / 10.0;

      const powerRaw = (bytes[11] << 8) | bytes[12];
      const power_kW = powerRaw / 100.0;

      const energy_kwh_x10 =
        ((bytes[13] << 24) >>> 0) |
        ((bytes[14] << 16) >>> 0) |
        ((bytes[15] << 8)  >>> 0) |
        ((bytes[16] << 0)  >>> 0);
      const energy_kWh = energy_kwh_x10 / 10.0;

      const flags = bytes[17];
      const energyMode = (flags & 0x01) ? "cooling" : "heating";
      const sensorLocation = (flags & 0x02) ? "return" : "flow";

      const powerKey = `power_kW_abs@${energyMode}`;
      const energyKey = `energy_kWh_inc@${energyMode}`;

      const result = {
        data: {
          payload_type: "sensor_data_v2_legacy",
          volumeFlow_m3perh_abs: volumeFlow_m3perh_abs,
          volume_m3_inc: volume_m3_inc,
          "temperature_degrC_abs@flow": temperature_degrC_abs_flow,
          "temperature_degrC_abs@return": temperature_degrC_abs_return,
          sensor_location: sensorLocation
        }
      };

      result.data[powerKey] = power_kW;
      result.data[energyKey] = energy_kWh;

      return result;
    } else {
      return {
        errors: [`Unexpected payload length: ${bytes.length} bytes`]
      };
    }
  }

  // ===== UNKNOWN PAYLOAD TYPE =====
  return {
    data: {
      payload_type: "unknown",
      raw_type: type
    },
    warnings: [`Unknown payload type: 0x${type.toString(16).toUpperCase()}`]
  };
}
```

### Step 3 - Device configuration

1. Power on the device
2. Hold PRG button for 3 seconds to start WiFi AP
3. Connect to WiFi:
   - **SSID:** `Heltec-XXXX` (last 4 hex of DevEUI)
   - **Password:** `configme` (default)
   - **URL:** `192.168.4.1`
4. Configure LoRaWAN credentials (Tab "LoRaWAN"):
   - Enter AppEUI and AppKey matching your TTN registration
5. Configure sensor sources (Tab "Sensor-Quellen"):
   - Select analog or Modbus for each measurement
6. Save and the device will join automatically

---

## Payload Structure (17 Bytes)

| Byte | Description | Unit | Format |
|------|-------------|------|--------|
| 0-2 | Type + Temperatures (2x 10-bit) | °C x 10 | Bit-packed |
| 3-4 | Flow x 10 | l/min | uint16 BE |
| 5-8 | Volume | Liters | uint32 BE |
| 9-11 | Power | Watts | uint24 BE |
| 12-15 | Energy x 10 | Wh | uint32 BE |
| 16 | Flags (Mode/Location) | - | uint8 |

**Flags:**
- Bit 0: Energy Mode (0=Heating, 1=Cooling)
- Bit 1: Sensor Location (0=Flow, 1=Return)

**Invalid Value Indicators:**
- Temperature: 1023 (0x3FF)
- Flow: 65535 (0xFFFF)
- Volume: 4294967295 (0xFFFFFFFF)
- Power: 16777215 (0xFFFFFF)
- Energy: 4294967295 (0xFFFFFFFF)

---

## Optional Settings

### Change TX Interval (Transmission Frequency)

**Local (via WiFi Portal):**
1. Start WiFi AP (hold PRG 3s)
2. Tab "LoRaWAN" -> TX Interval
3. Set value (1-240 minutes, default: 5)
4. Save

**Remote (LoRaWAN Downlink):**
```
Port: 10
Payload: 01 0F  (15 minutes)
         01 1E  (30 minutes)
         01 3C  (60 minutes)
```

### Set Energy Mode (Heating/Cooling)

**Local (via WiFi Portal):**
1. Tab "Sensor-Quellen" -> Energie-Modus
2. Select Heating or Cooling

**Effect:**
- **Heating:** dT = T_Supply - T_Return
- **Cooling:** dT = T_Return - T_Supply

### Clamp-on Sensor Correction

Compensates systematic measurement error of surface-mounted PT100 sensors.

**Formula:** `T_corr = (T_meas - k * T_room) / (1 - k)`

**Configuration (WiFi Portal):**
- Tab "Sensor-Quellen" -> Rohranlegefühler-Korrektur
- k-Factor: 0.01 - 0.20 (default: 0.05)
- Room Temperature: 5 - 40°C (default: 24°C)

### Reset Counters

**Remote:**
```
Port: 10
Payload: 02
```

### Query Configuration

**Remote:**
```
Port: 10
Payload: 05
```
Response (5 bytes): `85 [TxInt] [Flags] [FlowMax/10] 00`

---

## Downlink Commands (Port 10)

| CMD | Bytes | Description | Response |
|-----|-------|-------------|----------|
| `0x01` | `01 [min]` | Set TX interval (1-240 min) | `81 [min]` |
| `0x02` | `02` | Reset counters | `82` + uplink |
| `0x05` | `05` | Query configuration | `85 [TxInt] [Flags] [FlowMax/10] 00` |

---

## Calibration

### 2-Point Calibration (4-20mA Signal)

**Why needed?**
- Compensates tolerances in the current loop and shunt resistor

**Procedure (via WiFi Portal):**

1. Start WiFi AP (hold PRG 3s)
2. Go to Tab "4-20mA Konfiguration"
3. Set flow sensor to output **4.00 mA** (test mode)
4. Read the live raw value and enter as "4mA calibration point"
5. Set flow sensor to output **20.00 mA**
6. Read the live raw value and enter as "20mA calibration point"
7. Save - values are stored in NVS (permanent)

---

## Troubleshooting

### "Join FAIL" / No join
- Check LoRaWAN credentials (DevEUI/AppEUI/AppKey) match TTN registration
- Verify gateway coverage
- Check antenna connection (external antenna must be screwed on tight)
- Keep away from interference sources (water heaters, electrical panels)
- Auto-retry every 60 seconds, timeout after 120 seconds

### Sensor Errors on Display (System Page)
- **ADC --:** Check 4-20mA wiring, ADS1115 I2C connection (3.3V, Addr 0x48)
- **PT100 VL/RL --:** Check 4-wire PT100 connection (F+/RTD+/RTD-/F-), check RREF matches board (430 Ohm for PT100)
- **Modbus Err/Backoff:** Check RS485 wiring (A-A, B-B, GND-GND), slave address, baud rate

### Serial Monitor shows nothing
- **USB CDC On Boot** must be set to **Enabled** in Arduino IDE
- GPIO 19/20 are USB pins - code must not reconfigure them
- Baud rate: 115200

### Display stays dark
- Press PRG or PAGE button to wake from standby
- Standby activates after 60s inactivity, display off after additional 30s

---
