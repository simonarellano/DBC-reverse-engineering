# 🚗 VW Passat CC 2012 — CAN Bus Reverse Engineering

## Vehicle Information

| Field | Detail |
|---|---|
| Make | Volkswagen |
| Model | Passat CC |
| Year | 2012 |
| CAN Protocol | CAN 2.0B (High-Speed, 500 kbps) |
| Analysis Status | 🔬 In progress |

---

## 📂 Folder Contents

| Folder / File | Description |
|---|---|
| [`docs/engine.md`](./docs/engine.md) | Findings for CAN IDs related to the engine |
| [`docs/transmission.md`](./docs/transmission.md) | Findings for CAN IDs related to the transmission |
| [`dbc/passat_cc_2012_v1.dbc`](./dbc/passat_cc_2012_v1.dbc) | DBC file with identified signals so far |
| [`scripts/`](./scripts/) | Capture and analysis scripts |

---

## 🛠️ Tools Used

- **SavvyCAN** for frame capture.
- **cantools** (Python) for signal decoding and validation.
- CAN Interface: *[fill in with the adapter used, e.g. Peak PCAN-USB]*

---

## 📝 General Notes

- The high-speed bus (HS-CAN) operates at **500 kbps**.
- The documented IDs were captured with the vehicle under various driving conditions.
- DBC files are working versions and may contain unvalidated signals.

---

## 🔗 References

- [Ross-Tech Wiki — VW/Audi CAN buses](https://wiki.ross-tech.com/)
- [OpenGarages — Vehicle Hacker's Handbook](https://opengarages.org/)
