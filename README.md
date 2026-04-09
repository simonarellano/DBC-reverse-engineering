# DBC Reverse Engineering — CAN Bus

Repositorio colaborativo para documentar hallazgos de ingeniería inversa sobre las líneas CAN bus de distintos vehículos. El objetivo principal es capturar, decodificar y compartir el significado de los mensajes CAN (IDs, señales, valores) de cada vehículo analizado.

---

## Objetivos

- **Documentar** los IDs CAN y las señales identificadas en cada vehículo (motor, transmisión, carrocería, etc.).
- **Centralizar** los archivos DBC generados/modificados para cada modelo de vehículo.
- **Proveer scripts** de apoyo (Python, C++) que faciliten la captura, filtrado y análisis de tramas CAN.
- **Fomentar** el conocimiento abierto sobre los protocolos internos de los vehículos modernos.

---

## 📁 Estructura del repositorio

```
DBC-reverse-engineering/
│
├── <vehiculo>/              # Una carpeta por cada vehículo analizado
│   ├── docs/                # Documentación Markdown (hallazgos por ID o sistema)
│   │   ├── motor.md
│   │   └── transmision.md
│   ├── dbc/                 # Archivos DBC oficiales y sus versiones
│   │   └── vehiculo_v1.dbc
│   ├── scripts/             # Scripts de análisis (Python, C++, etc.)
│   └── README.md            # Portada del vehículo: resumen, herramientas y estado
│
└── README.md                # Este archivo — visión general del proyecto
```

---

## Vehículos disponibles

| Vehículo | Carpeta | Estado |
|---|---|---|
| VW Passat CC 2012 | [`vw-passat-cc-2012/`](./vw-passat-cc-2012/) | En progreso |


## Herramientas recomendadas

- **[SavvyCAN](https://github.com/collin80/SavvyCAN)** — captura y visualización de tramas CAN.
- **[cantools](https://github.com/eerimoq/cantools)** — manipulación y decodificación de archivos DBC en Python.
- **[python-can](https://python-can.readthedocs.io/)** — librería Python para interfaces CAN.
---
