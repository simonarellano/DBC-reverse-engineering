# 🔧 Engine — VW Passat CC 2012

Documentation of CAN IDs and signals related to the engine system.

> **Status:** 🔬 In progress — signals marked with ⚠️ have not yet been fully validated.

---

## Identified IDs

### ID `0x280` — Engine RPM and Load  ⚠️

| Start Bit | Length | Factor | Offset | Unit | Signal |
|---|---|---|---|---|---|
| 24 | 16 | 0.25 | 0 | rpm | EngineRPM |
| 8  | 8  | 0.392 | 0 | % | EngineLoad |

**Example frame:**
```
0x280  8  00 00 1F 40 00 64 00 00
```
→ RPM = `0x1F40` × 0.25 = **2000 rpm**
→ Load = `0x64` × 0.392 ≈ **38.8 %**

---

### ID `0x320` — Coolant Temperature ⚠️

| Start Bit | Length | Factor | Offset | Unit | Signal |
|---|---|---|---|---|---|
| 0 | 8 | 1 | -40 | °C | CoolantTemp |

---

### ID `0x380` — Vehicle Speed

| Start Bit | Length | Factor | Offset | Unit | Signal |
|---|---|---|---|---|---|
| 0 | 16 | 0.01 | 0 | km/h | VehicleSpeed |

---

## Notes

- Capture was performed at idle and during acceleration in 2nd gear.
- Pending: identify throttle position signal (APP).
- Pending: validate turbo boost pressure ID.

---

## Change History

| Date | Change |
|---|---|
| 2024-01 | Initial identification of RPM and speed |
