# DBC Reverse Engineering — CAN Bus

Repository for documenting reverse engineering findings on the CAN bus lines of various vehicles. The main goal is to capture, decode, and share the meaning of CAN messages (IDs, signals, values) for each analyzed vehicle.

---

## Goals

- **Document** CAN IDs and identified signals in each vehicle (engine, transmission, body, etc.).
- **Centralize** the DBC files generated/modified for each vehicle model.
- **Provide support scripts** (Python, C++) to facilitate the capture, filtering, and analysis of CAN frames.
- **Promote** open knowledge about the internal protocols of modern vehicles.

---

## 📁 Repository Structure

```
DBC-reverse-engineering/
│
├── <vehicle>/               # One folder per analyzed vehicle
│   ├── docs/                # Markdown documentation (findings by ID or system)
│   │   ├── engine.md
│   │   └── transmission.md
│   ├── dbc/                 # Official DBC files and their versions
│   │   └── vehicle_v1.dbc
│   ├── scripts/             # Analysis scripts (Python, C++, etc.)
│   └── README.md            # Vehicle overview: summary, tools, and status
│
└── README.md                # This file — project overview
```

---

## Available Vehicles

| Vehicle | Folder | Status |
|---|---|---|
| VW Passat CC 2012 | [`vw-passat-cc-2012/`](./vw-passat-cc-2012/) | In progress |


## Recommended Tools

- **[SavvyCAN](https://github.com/collin80/SavvyCAN)** — CAN frame capture and visualization.
- **[cantools](https://github.com/eerimoq/cantools)** — DBC file manipulation and decoding in Python.
- **[python-can](https://python-can.readthedocs.io/)** — Python library for CAN interfaces.
---
