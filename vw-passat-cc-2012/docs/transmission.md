# Transmission — VW Passat CC 2012

Documentation of CAN IDs and signals related to the DSG automatic transmission (DQ250).

> **Status:** In progress — signals marked with ⚠️ have not yet been fully validated.

---

## Identified IDs

### ID `0x540` — Current Gear and Transmission Mode

| Start Bit | Length | Factor | Offset | Unit | Signal |
|---|---|---|---|---|---|
| 0 | 4 | 1 | 0 | — | CurrentGear |
| 4 | 4 | 1 | 0 | — | TargetGear |
| 8 | 2 | 1 | 0 | — | TransmissionMode |

**`TransmissionMode` values:**

| Value | Description |
|---|---|
| 0 | Park / Neutral |
| 1 | Drive |
| 2 | Sport |
| 3 | Manual (Tiptronic) |

---

### ID `0x560` — Gearbox Oil Temperature ⚠️

| Start Bit | Length | Factor | Offset | Unit | Signal |
|---|---|---|---|---|---|
| 0 | 8 | 1 | -40 | °C | GearboxOilTemp |

---

### ID `0x580` — Shift Lever Position ⚠️

| Start Bit | Length | Factor | Offset | Unit | Signal |
|---|---|---|---|---|---|
| 0 | 8 | 1 | 0 | — | ShiftLeverPosition |

**Known values:** `0x50` = P, `0x60` = R, `0x70` = N, `0x80` = D

---

## Notes

- The transmission is a 6-speed DSG (DQ250) with wet dual clutch.
- Pending: identify transmitted torque signal.
- Pending: validate clutch temperature IDs.

---

## Change History

| Date | Change |
|---|---|
| 2024-01 | Initial identification of current gear and mode |
