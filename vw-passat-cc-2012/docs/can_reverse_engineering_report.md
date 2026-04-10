# CAN Reverse Engineering Report
## VW Passat CC 2012 — DQ250 DSG — 500 kbps HS-CAN

**Capture tool:** cangaroo / canAble (firmware slcan) on COM7  
**Network:** Post-gateway broadcast (full vehicle CAN)  
**Protocol:** CAN 2.0B, 11-bit IDs, 500 kbps

---

## 1. Log Files Analyzed

| File | Condition | Duration | Frames |
|------|-----------|----------|--------|
| `760_RPM.log` | Engine at ~760 RPM idle (vcan0 replay) | ~51 s | ~49 k |
| `2000_RPM.log` | Pedal pressed → engine to ~2000 RPM, then released | ~82 s | ~82 k |
| `760-3900-RPM.log` | Long idle then pedal pressed → ~3900 RPM peak | ~108 s | ~108 k |

**Key observation in 0x280 B0:** `0x01` = pedal released, `0x00` = pedal pressed (user-confirmed).

**Physical sensor reference values provided:**

| Sensor | Pedal released | Pedal pressed (2000 RPM test) |
|--------|---------------|-------------------------------|
| APP Sensor 1 | 14.84 % | 80.86 % |
| APP Sensor 2 | 7.42 % | 40.23 % |
| TPS Sensor 1 | 12.89 % | 87.11 % |
| TPS Sensor 2 | 83.59 % | 12.11 % |

---

## 2. Complete ID Summary

| ID | DLC | Freq (Hz) | Variable Bytes | Description |
|----|-----|-----------|----------------|-------------|
| `0x050` | 4 | 50 | B2–B3 | Unknown, low-freq counter |
| `0x1A0` | 8 | 100 | B7 | ECU / ignition — rolling counter only |
| `0x1AC` | 8 | 50 | B0, B1, B6 | Transmission/ECU status |
| **`0x280`** | **8** | **100** | **B0–B7** | **Engine ECU — RPM, APP sensors** |
| `0x288` | 8 | 50 | B0, B1, B5, B6, B7 | ABS/ESP module |
| `0x2A0` | 8 | 100 | B6, B7 | ECU — counter bytes only |
| `0x2C3` | 1 | 10 | — | Static (heartbeat) |
| `0x320` | 8 | 50 | B5 | ABS/ESP — rolling counter + static data |
| `0x343` | 3 | 10 | — | Static configuration |
| `0x35F` | 8 | 10 | — | Static status (settles after startup) |
| `0x373` | 6 | 10 | — | Static |
| `0x393` | 8 | 5 | — | Static |
| `0x3D0` | 6 | 50 | — | Static (all tests: `00800800c866`) |
| `0x3E1` | 8 | 10 | B2 | ECU status — slowly varying |
| `0x3E3` | 8 | 1 | — | Static / diagnostic |
| `0x420` | 8 | 5 | — | Static |
| `0x470` | 5 | 20 | — | Static (mostly `0001000010`) |
| **`0x480`** | **8** | **50** | **B0, B2, B3, B7** | **DSG input — high-freq signals, partial decode** |
| **`0x48A`** | **8** | **50** | **B0, B1, B2, B7** | **DSG input — TPS Sensor 1** |
| `0x520` | 8 | 5 | B0, B1 | Slowly varying |
| `0x531` | 4 | 20 | B2, B3 | Counter / encoding |
| `0x540` | 8 | 100 | B0, B7 | Gateway — rolling counter |
| `0x544` | 8 | 10 | B7 | Mostly static + counter |
| `0x550` | 8 | 5 | — | Static after startup |
| **`0x555`** | **8** | **50** | **B1, B3, B4** | **Unknown — B1 changes with throttle** |
| `0x557` | 8 | 2 | — | Static |
| `0x56A` | 8 | 25 | B0, B7 | Heartbeat — XOR checksum counter |
| `0x575` | 4 | 5 | — | Static |
| `0x580` | 8 | 1 | B0, B3–B7 | Diagnostic / slow update |
| `0x58C` | 8 | 10 | B1, B4 | Slowly varying |
| `0x58F` | 8 | 2 | B7 | Mostly static + counter |
| `0x5A0` | 8 | 50 | B0, B3 | Rolling counter only |
| `0x5C0` | 8 | 50 | B0, B7 | Rolling counter only |
| `0x5D0` | 8 | 10 | — | Static |
| `0x5F3` | 8 | 2 | — | Static |
| `0x60E` | 2 | 1 | — | Static |
| `0x621` | 7 | 10 | B6 | Status broadcast — probable coolant temp |
| `0x62E` | 7 | 1 | B5, B6 | Slowly varying |
| `0x62F` | 4 | 5 | — | Static |
| `0x631` | 4 | 1 | — | Static |
| `0x635` | 3 | 5 | — | Static |
| `0x651` | 8 | 10 | — | Static |
| `0x655` | 8 | 2 | — | Static |
| `0x657` | 8 | 20 | — | Static (after startup) |
| `0x658` | 8 | 10 | B5, B6 | Slowly varying |
| `0x65D` | 8 | 1 | B6, B7 | Slowly varying |
| `0x661` | 8 | 1 | — | Static |
| `0x66C` | 3 | 1 | B0–B2 | Slowly varying |
| `0x677` | 8 | 1 | B1, B2, B4, B6, B7 | Slowly varying |
| `0x67C` | 2 | <1 | — | Rare / diagnostic |
| `0x67D` | 3 | 1 | B0–B2 | Slowly varying |
| `0x6C8` | 2 | 2 | B0, B1 | Slowly varying |
| `0x720` | 7 | 10 | — | Static (UDS/KWP diagnostic?) |
| `0x727` | 7 | 5 | — | Static (UDS/KWP diagnostic?) |

> IDs present only during startup (not in steady state): `0x010`, `0x220`, `0x221`, `0x248`, `0x329`, `0x338`, `0x4D0`, `0x5C1`, `0x6C9`

---

## 3. Identified Signals

### 3.1 ID `0x280` — Engine ECU (100 Hz, DLC=8)

**Confidence: HIGH**

| Signal | Start Bit | Length | Byte Order | Factor | Offset | Unit | Notes |
|--------|-----------|--------|------------|--------|--------|------|-------|
| `PedalState` | 0 | 8 | Intel | 1 | 0 | flag | `0x01`=released, `0x00`=pressed |
| `APP_Sensor2` | 8 | 8 | Intel | 3.3183 | -35.72 | % | Accel. pedal pos. sensor 2 |
| `EngineRPM` | 16 | 16 | Intel | 0.25 | 0 | rpm | 16-bit LE: bytes[2] LSB + bytes[3] MSB |
| `APP_Sensor2_Mirror` | 32 | 8 | Intel | 3.3183 | -35.72 | % | Redundant copy of APP2 (B4 ≈ B1) |
| `APP_Sensor1` | 40 | 8 | Intel | 2.0396 | 14.84 | % | Accel. pedal pos. sensor 1 |

**Calibration verification:**

| Signal | Raw idle | Physical idle | Raw pressed | Physical pressed |
|--------|----------|--------------|-------------|-----------------|
| `EngineRPM` | 3032 (~`0x0BE8`) | ~758 rpm | 8000 (`0x1F40`) | ~2000 rpm |
| `APP_Sensor1` (B5) | 0 | 14.84 % | ~32 (mode=33) | 80.86 % |
| `APP_Sensor2` (B1) | 13 (mode) | 7.42 % | ~23 (mode=23) | 40.23 % |

**Important notes:**
- `EngineRPM`: verified at ~760, ~2000, and ~3900 rpm (raw × 0.25 matches observed values).
- `APP_Sensor1` (B5): raw value is **exactly 0** when pedal released; increases immediately when pressed. Calibration is based on two points (idle and 2000 RPM test). Maximum observed raw value = **153** at ~3900 RPM (full throttle); extrapolation beyond the calibrated range may not be linear.
- `APP_Sensor2` (B1) with mirror in B4: B1 and B4 differ in only ~1.2 % of frames — consistent with redundant APP sensor pattern used in VW safety systems. Calibration from same two-point method.
- **B3 is NOT an independent signal** — it is the MSB of the 16-bit RPM field. Its apparent correlation with APP2 is spurious (both increase when pedal is pressed).

**Example frame analysis:**
```
Pedal released, ~758 RPM:
01 0D E8 0B 0D 00 0A 05
B0=0x01 (released)  B1=0x0D=13 (APP2→7.42%)  B2-B3=0x0BE8 (RPM=748.0 rpm*)
B4=0x0D=13 (APP2 mirror) B5=0x00 (APP1→14.84%)

Pedal pressed, ~2000 RPM:
00 17 40 1F 17 21 0A 17
B0=0x00 (pressed)  B1=0x17=23 (APP2→40.54%)  B2-B3=0x1F40 (RPM=2000 rpm)
B4=0x17=23 (mirror)  B5=0x21=33 (APP1→82.17%)
```
*Minor discrepancy from averaging is normal; individual frames vary ±1 bit.

---

### 3.2 ID `0x48A` — DSG Transmission Input / TPS (50 Hz, DLC=8)

**Confidence: HIGH for TPS1 identification; MEDIUM for exact scaling**

| Signal | Start Bit | Length | Byte Order | Factor | Offset | Unit | Notes |
|--------|-----------|--------|------------|--------|--------|------|-------|
| `TPS_Sensor1` | 8 | 8 | Intel | 0.3990 | 10.89 | % | Throttle valve position sensor 1 |
| `TPS_Status` | 16 | 8 | Intel | 1 | 0 | flag | `0x01`=idle/closed, `0x02`=throttle open |
| `RollingCounter` | 60 | 4 | Intel | 1 | 0 | count | Upper nibble of B7; lower nibble always 0 |

**Calibration verification:**

| Signal | Raw idle | Physical | Raw pressed | Physical |
|--------|----------|----------|-------------|----------|
| `TPS_Sensor1` (B1) | 5 (mode) | 12.89 % | 191 (mode) | 87.11 % |

**Notes:**
- B7 increments by 16 (`0x10`) each message: `0x00, 0x10, 0x20, ..., 0xF0, 0x00 ...` → 4-bit counter in the upper nibble.
- B2 (`TPS_Status`) = `0x01` when pedal released, `0x02` when pedal pressed — may be the MSB extension of the TPS signal or a direction flag.
- At ~3900 RPM full throttle: B1=235 → TPS1 ≈ 104 % (slightly over physical max, likely sensor saturation or minor calibration error).
- **TPS Sensor 2 location not yet confirmed** — see Section 5.

---

### 3.3 Rolling Counters and Checksums

**Confidence: HIGH (fully verified)**

| ID | Byte | Pattern | Notes |
|----|------|---------|-------|
| `0x1A0` | B7 | Lower nibble 0x0–0xF, upper nibble constant `0x1` | 4-bit counter |
| `0x2A0` | B6–B7 | Cycling | Message counter |
| `0x320` | B5 | 0x00–0x0F sequential, increments each message | 4-bit counter |
| `0x48A` | B7 | Upper nibble 0x0–0xF (×16), lower nibble=0 | 4-bit counter |
| `0x540` | B0 | Upper nibble 0x0–0xF, lower nibble constant `0x4` | 4-bit counter |
| `0x56A` | B0, B7 | B0 = B7 always; XOR of all bytes = 0x00 | Counter + XOR checksum |
| `0x5A0` | B3 | Upper nibble 0x0–0xF (×16), lower nibble=0 | 4-bit counter |
| `0x5C0` | B0, B7 | B0: lower nibble 0x0–0xF, upper constant `0x4`; B7 similar | 4-bit counters |

---

### 3.4 Temperature Signals (Probable)

> **Confidence: MEDIUM** — values are constant across all tests (warm engine); a cold-start test is required to confirm the encoding `temp_°C = raw − 40`.

| ID | Byte | Raw Value | Decoded Temp | Probable Signal |
|----|------|-----------|--------------|-----------------|
| `0x320` | B2 | 133 | **93 °C** | Engine coolant temperature |
| `0x621` | B3 | 133 | **93 °C** | Engine coolant temperature (broadcast) |
| `0x1AC` | B4 | 105 | **65 °C** | Possible gearbox oil temperature |
| `0x3D0` | B5 | 102 | **62 °C** | Unknown temperature |
| `0x555` | B2 | 105 | **65 °C** | Possible gearbox oil temperature |
| `0x5D0` | B2 | 105 | **65 °C** | Possible gearbox oil temperature |
| `0x651` | B2 | 105 | **65 °C** | Possible gearbox oil temperature |

> The value **133** (→ 93 °C) appears in two independent IDs (0x320 and 0x621), reinforcing that it represents coolant temperature at normal operating temperature. The value **105** (→ 65 °C) appears in multiple IDs and likely represents gearbox oil temperature.

---

### 3.5 Other Partially Decoded IDs

#### `0x480` — DSG / Engine (50 Hz, DLC=8)

- **B0**: Exactly 4 unique values (`0x00`, `0x40`, `0x80`, `0xC0`) → 2-bit counter in bits[7:6], lower 6 bits = part of signal.
- **B1**: Constant = `0x08` in all steady-state conditions.
- **B2, B3, B7**: Rapidly cycling (200+ unique values) → likely high-resolution analog signals (injection timing, boost, MAF, etc.).
- **Confidence: LOW** — signals cannot be identified without additional targeted tests.

#### `0x555` — Unknown (50 Hz, DLC=8)

- **B1**: 117 at idle → 70 at 2000 RPM pressed (decreases with throttle). Possible inverted signal.
- **B3**: 47 at idle → 58 at 2000 RPM pressed (increases with throttle).
- **B2**: Constant = 105 → possible temperature (see Section 3.4).
- **Confidence: LOW** — relationship to physical sensors unconfirmed.

#### `0x288` — ABS/ESP Module (50 Hz, DLC=8)

- Variable bytes: B0 (4 unique), B1 (4–7 unique), B5 (2 unique), B6 (68 unique), B7 (7–19 unique).
- **Vehicle speed likely present here** but cannot be isolated: car was stationary in all tests; all wheel-speed bytes remain near-constant.
- **Confidence: LOW** for signal identification; needs driving-speed test.

---

## 4. Signals NOT Identified (Cannot Determine Without Additional Tests)

| Signal | Likely ID | Reason Not Identified |
|--------|-----------|----------------------|
| Vehicle speed | `0x288` or similar | Car was stationary in all 3 tests |
| Current gear (1–6) | `0x480` or `0x48A` | Car in P/N throughout tests; no gear changes |
| Selector position (P/R/N/D/S) | Unknown | Same reason as above |
| Engine coolant temp (dynamic) | `0x320`, `0x621` | Value constant (warm engine); no cold-start test |
| Gearbox oil temp (dynamic) | `0x1AC`, `0x555` | Value constant; no cold-to-warm transition |
| Brake signal | Unknown | Brake was not explicitly exercised |
| TPS Sensor 2 | Unknown | No candidate with clear anti-correlation to TPS1 |
| Engine oil pressure/temp | Unknown | No variation in available tests |
| Turbo boost pressure | Unknown | Insufficient variation in test conditions |
| Steering angle | Unknown | No steering input in tests |

---

## 5. Signal Confidence Summary

| Signal | ID | Start Bit | Length | Factor | Offset | Unit | Confidence |
|--------|----|-----------|--------|--------|--------|------|------------|
| PedalState | 0x280 | 0 | 8 | 1 | 0 | flag | ✅ HIGH |
| EngineRPM | 0x280 | 16 | 16 | 0.25 | 0 | rpm | ✅ HIGH |
| APP_Sensor1 | 0x280 | 40 | 8 | 2.04 | 14.84 | % | ✅ HIGH (⚠️ scaling limited to tested range) |
| APP_Sensor2 | 0x280 | 8 | 8 | 3.32 | -35.72 | % | ✅ HIGH (⚠️ same caveat) |
| APP_Sensor2_Mirror | 0x280 | 32 | 8 | 3.32 | -35.72 | % | ✅ HIGH |
| TPS_Sensor1 | 0x48A | 8 | 8 | 0.399 | 10.89 | % | ✅ HIGH |
| TPS_Status | 0x48A | 16 | 8 | 1 | 0 | flag | ⚠️ MEDIUM |
| CoolantTemp | 0x320 | 16 | 8 | 1 | -40 | °C | ⚠️ MEDIUM (needs cold test) |
| CoolantTemp_BCM | 0x621 | 24 | 8 | 1 | -40 | °C | ⚠️ MEDIUM (needs cold test) |
| GearboxOilTemp | 0x1AC | 32 | 8 | 1 | -40 | °C | ⚠️ MEDIUM (needs cold test) |
| VehicleSpeed | — | — | — | — | — | km/h | ❌ NOT FOUND |
| CurrentGear | — | — | — | — | — | — | ❌ NOT FOUND |
| SelectorPosition | — | — | — | — | — | — | ❌ NOT FOUND |
| TPS_Sensor2 | — | — | — | — | — | % | ❌ NOT FOUND |

---

## 6. Methodology Notes

- **Calibration method:** Two-point linear regression using the provided physical sensor values (idle and "pedal pressed during 2000 RPM test") as ground truth.
- **Stable idle reference:** The `760-3900-RPM.log` was used for idle calibration (pedal fully released, RPM ≈ 758, several thousand frames). The `760_RPM.log` (vcan0 replay) contains a warm-up sequence that distorts averages and was used only for cross-validation.
- **APP sensor factor note:** The APP factors were derived from only two calibration points. The maximum observed raw value for APP1 (153 at 3900 RPM) extrapolates beyond 100 % with the current formula — this indicates either a non-linear sensor curve or the formula is only accurate in the tested throttle range (idle → ~80 % of full travel). **Additional intermediate calibration points are strongly recommended before writing the final DBC.**
- **Counter identification:** All identified counters were verified by confirming constant step increments across 50+ consecutive frames.
- **Checksum in 0x56A:** Verified across 500+ frames: XOR of all 8 bytes = 0x00 in every frame.

---

*Generated: 2026-04-10 — Based on logs from testing branch*
