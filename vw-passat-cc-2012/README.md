# 🚗 VW Passat CC 2012 — CAN Bus Reverse Engineering

## Información del vehículo

| Campo | Detalle |
|---|---|
| Marca | Volkswagen |
| Modelo | Passat CC |
| Año | 2012 |
| Protocolo CAN | CAN 2.0B (High-Speed, 500 kbps) |
| Estado del análisis | 🔬 En progreso |

---

## 📂 Contenido de esta carpeta

| Carpeta / Archivo | Descripción |
|---|---|
| [`docs/motor.md`](./docs/motor.md) | Hallazgos de los IDs CAN relacionados con el motor |
| [`docs/transmision.md`](./docs/transmision.md) | Hallazgos de los IDs CAN relacionados con la transmisión |
| [`dbc/passat_cc_2012_v1.dbc`](./dbc/passat_cc_2012_v1.dbc) | Archivo DBC con las señales identificadas hasta el momento |
| [`scripts/`](./scripts/) | Scripts de captura y análisis |

---

## 🛠️ Herramientas utilizadas

- **SavvyCAN** para la captura de tramas.
- **cantools** (Python) para decodificación y validación de señales.
- Interfaz CAN: *[completar con el adaptador utilizado, p.ej. Peak PCAN-USB]*

---

## 📝 Notas generales

- El bus de alta velocidad (HS-CAN) opera a **500 kbps**.
- Los IDs documentados se capturaron con el vehículo en distintas condiciones de marcha.
- Los archivos DBC son versiones de trabajo y pueden contener señales sin validar.

---

## 🔗 Referencias

- [Ross-Tech Wiki — VW/Audi CAN buses](https://wiki.ross-tech.com/)
- [OpenGarages — Vehicle Hacker's Handbook](https://opengarages.org/)
