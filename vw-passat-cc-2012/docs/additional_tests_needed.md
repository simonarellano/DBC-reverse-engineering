# Additional Tests Needed
## VW Passat CC 2012 — DQ250 DSG — CAN Reverse Engineering

This document lists the capture sessions required to identify and validate the signals that could not be decoded from the existing logs.

---

## Priority 1 — Essential Tests (Cannot Build Complete DBC Without These)

### TEST-01: Vehicle Speed at Known Speeds

**Goal:** Identify the vehicle speed signal and its encoding.

**Procedure:**
1. Drive on a straight flat road.
2. Hold each target speed steady for at least 30 seconds:
   - 0 km/h (vehicle completely stopped, engine running)
   - 20 km/h
   - 40 km/h
   - 80 km/h
   - 120 km/h (if safe and legal)
3. Label each file with the speed: e.g., `20_kmh.log`, `80_kmh.log`.

**Target IDs to verify:** `0x288`, `0x5C0`, `0x5A0`, `0x320`  
**Expected encoding:** 16-bit LE, factor ≈ 0.01 km/h/bit (common VW ABS encoding)

---

### TEST-02: Gear Changes (All Gears + All Selector Positions)

**Goal:** Identify current gear, target gear, and selector position (P/R/N/D/S/Sport).

**Procedure A — Selector position (stationary):**
1. With engine running and brakes applied, cycle through: **P → R → N → D → S → Manual(+/−)**.
2. Hold each position for 10–15 seconds.
3. Name file: `selector_PRND.log`

**Procedure B — Gear changes while driving:**
1. Drive in D mode, accelerate gradually from 0 to ~80 km/h so the DSG shifts through gears 1–6.
2. Then decelerate to 0 (downshifts).
3. Name file: `gear_changes_1_to_6.log`

**Procedure C — Manual (tiptronic) mode:**
1. Select manual mode, manually step through gears 1–6 with engine at ~1500 RPM (light load).
2. Name file: `manual_gear_1_to_6.log`

**Target IDs to verify:** `0x480`, `0x48A`, `0x540`, `0x1AC`  
**Expected:** A byte or nibble that changes discretely with each gear (values 1–6 + N/R/P flags)

---

### TEST-03: Cold Engine Start — Temperature Signals

**Goal:** Confirm and calibrate the temperature signals (coolant, gearbox oil) using the `raw − 40` encoding.

**Procedure:**
1. Start the engine **cold** (after at least 4 hours of inactivity, ambient temp < 30 °C).
2. Begin logging **before** starting the engine.
3. Record continuously until coolant temp stabilizes (~90–95 °C), approximately 5–10 minutes.
4. Note the ambient temperature at the start.
5. Name file: `cold_start_warmup.log`

**Target IDs to verify:** `0x320` B2, `0x621` B3, `0x1AC` B4, `0x555` B2  
**Expected:** Byte values should start near `ambient_°C + 40` and rise to ~130–136 (≈ 90–96 °C)

---

## Priority 2 — Important for Completeness

### TEST-04: Brake Signal

**Goal:** Identify the brake pedal status bit.

**Procedure:**
1. Engine running, vehicle stationary.
2. Alternate between: **brake fully released → brake fully pressed → released** (5–10 cycles).
3. Hold each state for at least 5 seconds.
4. Name file: `brake_pressed_released.log`

**Target IDs to verify:** `0x1A0`, `0x320`, `0x35F`, `0x3E1`  
**Expected:** A single bit that toggles when brake is pressed

---

### TEST-05: Additional APP/TPS Calibration Points

**Goal:** Determine accurate scaling factors for APP1, APP2, and TPS1 across the full pedal range, and confirm TPS2 location.

**Procedure:**
1. Engine running, vehicle stationary (in N or P).
2. Slowly and steadily press the accelerator pedal to:
   - 25 % of full travel (hold 10 s)
   - 50 % of full travel (hold 10 s)
   - 75 % of full travel (hold 10 s)
   - 100 % of full travel (hold 5 s)
3. Use a scan tool (VCDS or OBD2 tool) to record the physical APP1, APP2, TPS1, TPS2 percentage at each step at the **same time** as the CAN log.
4. Name file: `app_tps_calibration_25_50_75_100.log`

**Notes:** If a scan tool is not available, the idle and 2000 RPM calibration values already provided can serve as 2-point calibration — but the formula will only be accurate near those two points.

---

### TEST-06: Engine Temperature Extremes (Overtemp / Cold Idle)

**Goal:** Validate temperature encoding at values different from 90–95 °C.

**Procedure:**
1. Log during the first 30 seconds of a cold start (see TEST-03, this can be the same file).
2. Separately: log for 2 minutes after the engine has been running hard and verify temperature remains stable.

---

## Priority 3 — Nice to Have

### TEST-07: Turbo Boost Pressure

**Goal:** Identify boost pressure signal in `0x480` or another ID.

**Procedure:**
1. Drive at moderate speed, hold steady at 2000 rpm with light load (no boost).
2. Accelerate hard (WOT) from 1500 to 4000 rpm in 3rd gear.
3. Name file: `boost_full_throttle_3rd_gear.log`

---

### TEST-08: Steering Angle

**Goal:** Identify steering angle signal.

**Procedure:**
1. Vehicle stationary, engine running.
2. Slowly turn steering wheel lock-to-lock: center → full left → center → full right → center.
3. Name file: `steering_lock_to_lock.log`

**Target IDs:** `0x35F`, `0x373`, `0x2C3` (or ABS module IDs)

---

### TEST-09: Reverse Gear / Parking Sensors

**Goal:** Identify reverse gear flag and parking sensor data.

**Procedure:**
1. With brakes applied, shift from P to R, hold 15 s, shift back to P.
2. Name file: `shift_to_reverse.log`

---

## Test Execution Notes

- **Always capture for at least 10 seconds before and after each event** (transition + stable state).
- **File naming convention:** use underscores, describe the action and parameter value. Examples:
  - `40_kmh_cruise.log`
  - `gear3_manual.log`
  - `brake_held_10s.log`
- **Same capture setup:** cangaroo / canAble (COM7), 500 kbps, same capture settings.
- **Repeat each test 2–3 times** if possible to verify reproducibility.

---

## Summary Table

| Test ID | Priority | Signals to Identify | Min Duration |
|---------|----------|-------------------|--------------|
| TEST-01 | 🔴 Essential | Vehicle speed | 5+ min |
| TEST-02 | 🔴 Essential | Gear, selector position | 5+ min |
| TEST-03 | 🔴 Essential | Coolant temp, gearbox oil temp | 10+ min |
| TEST-04 | 🟠 Important | Brake signal | 2+ min |
| TEST-05 | 🟠 Important | APP/TPS full calibration | 5+ min |
| TEST-06 | 🟡 Useful | Temperature validation | 2+ min |
| TEST-07 | 🟡 Useful | Boost pressure | 3+ min |
| TEST-08 | 🟡 Useful | Steering angle | 2+ min |
| TEST-09 | 🟡 Useful | Reverse / parking | 2+ min |

---

*Generated: 2026-04-10 — Complement to `can_reverse_engineering_report.md`*
